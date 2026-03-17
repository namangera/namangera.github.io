---
layout: post
title: "Enabling NVIDIA GPU Support in Podman Containers on Windows"
date: 2026-03-16
category: Programming
---

**Infrastructure · GPU · Setup Guide · Windows + WSL2**

Running GPU-accelerated workloads inside containers on Windows requires a small amount of setup to bridge the NVIDIA driver stack from the Windows host through WSL2 into your Podman machine. This guide walks through that setup end-to-end — from toolkit installation to running your first GPU-aware container.

## Before you begin

- NVIDIA GPU with up-to-date drivers installed on Windows
- Podman Desktop or Podman CLI installed
- WSL2 enabled and configured

---

## Step 1 — Install the NVIDIA Container Toolkit

The NVIDIA Container Toolkit makes the host GPU accessible to containers via CDI (Container Device Interface). You'll install it inside the Podman machine, not on Windows directly. Start by SSH-ing into the machine:
```bash
podman machine ssh
```

Then run the following to add the toolkit repo, install it, generate the CDI configuration, and verify the output:
```bash
curl -s -L https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo | \
  sudo tee /etc/yum.repos.d/nvidia-container-toolkit.repo && \
  sudo yum install -y nvidia-container-toolkit && \
  sudo nvidia-ctk cdi generate --output=/etc/cdi/nvidia.yaml && \
  nvidia-ctk cdi list
```

> **Verify:** The final command should output `nvidia.com/gpu=all`. If you see this entry, the toolkit is correctly installed and the CDI config has been generated.

Once verified, exit the Podman machine:
```bash
exit
```

---

## Step 2 — Test GPU Access from a Container

Before wiring up your full application stack, confirm the GPU is reachable. This command spins up a temporary CUDA container and runs `nvidia-smi` inside it:
```powershell
podman run --rm --device nvidia.com/gpu=all \
  nvidia/cuda:13.1.1-base-ubuntu22.04 nvidia-smi
```

> **Expected output:** A table listing your GPU name, driver version, and CUDA version. If that appears, GPU passthrough is working end-to-end.

---

## Step 3 — Configure Docker Compose for GPU Access

To make GPU access declarative in your Compose setup, add a `devices` reservation under the service's `deploy.resources` section. Here's an example `docker-compose-gpu.yml`:
```yaml
services:
  flowline-pipeline:
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [ gpu ]
```

This tells Podman Compose to pass all available NVIDIA GPUs into the container when the service starts, using the same CDI device interface set up in step 1.

---

## Step 4 — Run Your Pipeline

With the compose file in place, start your GPU-enabled stack:
```powershell
podman-compose -f .\docker-compose-gpu.yml up --build
```

The container will start with full access to the GPU as declared in the compose config.

---

## Troubleshooting

### `unresolvable CDI devices nvidia.com/gpu=all`

The NVIDIA Container Toolkit is either not installed or the CDI configuration file was not generated. Repeat step 1 in full, paying attention to whether `nvidia-ctk cdi list` returns the expected entry before exiting.

### `Found no NVIDIA driver on your system`

This usually means one of three things:

- The container doesn't have GPU access configured — check your Compose file for the `devices` block
- The NVIDIA drivers on the Windows host aren't accessible through WSL2
- The Podman machine was not restarted after the toolkit was installed

Try restarting the Podman machine to pick up the new configuration:
```powershell
podman machine stop
podman machine start
```

### GPU not visible inside container

Verify the CDI config file actually exists inside the Podman machine:
```bash
podman machine ssh
cat /etc/cdi/nvidia.yaml
exit
```

If the file is missing or empty, re-run the `nvidia-ctk cdi generate` command from step 1.

---

## How it works

**Driver location matters.** NVIDIA drivers must be installed on the Windows host — not inside WSL2. The WSL2 kernel exposes a `/dev/dxg` device that proxies GPU access from Windows.

**CDI is the bridge.** The file at `/etc/cdi/nvidia.yaml` in the Podman machine tells the container runtime exactly how to expose the GPU device. Without it, `--device nvidia.com/gpu=all` resolves to nothing.

**Passthrough chain.** GPU access flows: Windows drivers → WSL2 → Podman machine → container. Each layer must be correctly configured for the path to work end-to-end.

**Stable repo.** This guide uses the `/stable/rpm/` repository, which is compatible with newer Fedora-based Podman machine images.

---

## References

- [NVIDIA Container Toolkit — official documentation](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/index.html)
- [Podman for Windows — GPU support tutorial](https://github.com/containers/podman/blob/main/docs/tutorials/podman-for-windows.md#gpu-support)
