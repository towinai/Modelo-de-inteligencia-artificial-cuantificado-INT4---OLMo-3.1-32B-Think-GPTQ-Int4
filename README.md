# procedures-torquemada-llm-inference

> **Production-tested LLM inference deployment for sovereign on-premise infrastructure.  
> TORQUEMADA: OLMo-3.1-32B-Think INT4 with 65K context on two 16 GB GPUs.  
> Released to the community by TOWIN-IA — Zamora, Spain.**

[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)
[![vLLM](https://img.shields.io/badge/vLLM-0.33.0-green.svg)](https://github.com/vllm-project/vllm)
[![LiteLLM](https://img.shields.io/badge/LiteLLM-main--latest-orange.svg)](https://github.com/BerriAI/litellm)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791.svg)](https://www.postgresql.org/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

---

## 🇪🇸 Español

### ¿Qué es este repositorio?

Documentación operada en producción durante 2026 para el despliegue del
agente **TORQUEMADA** sobre el modelo **OLMo-3.1-32B-Think** cuantizado a
INT4, con contexto completo de **65.536 tokens**, sobre dos GPUs
consumer de 16 GB cada una.

Cubre el despliegue completo del stack de inferencia:

- **vLLM 0.33.0** como motor de inferencia (Tensor Parallel 2, KV cache FP8)
- **LiteLLM** como gateway OpenAI-compatible con autenticación por master key
- **PostgreSQL 16** como backend de auditoría y persistencia de logs
- Ajustes de sistema operativo (kernel, firewall, contenedores)
- Procedimientos de backup, verificación y diagnóstico

Publicado para la comunidad técnica por **TOWIN-IA** (TYBSI SL · Zamora,
España) como parte de nuestro catálogo de documentación de infraestructura
soberana.

### ¿Por qué publicamos esto?

Servir un modelo de 32 mil millones de parámetros con contexto extendido
de 65K tokens normalmente exige GPUs de datacenter de 80 GB o más. La
mayoría de operadores europeos que quieren construir capacidad de cómputo
de IA bajo su propio control no disponen de ese hardware.

Este repositorio documenta cómo conseguirlo con **hardware accesible**:

- Cuantización **INT4 W4A16** del modelo (16,9 GiB en disco)
- **Tensor Parallel 2** repartiendo pesos entre dos GPUs por PCIe
- **KV cache en FP8** que permite 234.570 tokens de caché en 32 GB de VRAM
- Ajustes de kernel (`vm.overcommit_memory=1`) para permitir el `mmap` del
  checkpoint en hosts con menos RAM que el tamaño del modelo

El resultado es un endpoint OpenAI-compatible, con doble capa de
autenticación y auditoría completa, desplegado íntegramente con Docker
Compose y reproducible en cualquier entorno con el hardware mínimo.

### Estado de la infraestructura

| Período | Estado |
|---|---|
| 2025–2026 | Desarrollo y validación del despliegue BF16 (referencia inicial) |
| Noviembre 2026 | Migración a INT4 con vLLM + LiteLLM + PostgreSQL |
| Actual | TORQUEMADA INT4 operativo · 65K contexto · 2× RTX 2000 Ada |

### Cobertura de las guías

- ✅ Despliegue vLLM con checkpoint `compressed-tensors` W4A16
- ✅ Tensor Parallel 2 sobre GPUs sin NVLink
- ✅ KV cache FP8 con concurrencia 3,58× a 65K tokens
- ✅ Ajuste de kernel (`vm.overcommit_memory`) para `mmap` de checkpoints grandes
- ✅ Gateway LiteLLM con master key y `api_base` interno
- ✅ PostgreSQL 16 con 232 migraciones Prisma de LiteLLM
- ✅ Doble capa de autenticación (cliente → LiteLLM → vLLM)
- ✅ Firewall UFW con exposición mínima
- ✅ Procedimiento de backup completo (compose, config, inspect, dump SQL)
- ✅ Diagnóstico de fallos (`Cannot allocate memory`, NCCL, OOM de KV)
- ✅ Suite de 5 pruebas funcionales de validación
- ✅ Notas de hardware para hosts GPU de gama media

---

## 🇬🇧 English

### What is this repository?

Production-operated documentation from 2026 for deploying the
**TORQUEMADA** agent on **OLMo-3.1-32B-Think** quantized to INT4, with
full **65,536-token context**, on two 16 GB consumer GPUs.

It covers the full inference stack deployment:

- **vLLM 0.33.0** as the inference engine (Tensor Parallel 2, FP8 KV cache)
- **LiteLLM** as an OpenAI-compatible gateway with master key auth
- **PostgreSQL 16** as the audit and log-persistence backend
- Operating-system tuning (kernel, firewall, containers)
- Backup, verification and diagnostic procedures

Released to the technical community by **TOWIN-IA** (TYBSI SL · Zamora,
Spain) as part of our sovereign-infrastructure documentation catalogue.

### Why we are publishing this

Serving a 32-billion-parameter model with 65K extended context normally
requires 80 GB or larger datacenter GPUs. Most European operators who
want to build AI compute capacity under their own control do not have
that hardware.

This repository documents how we did it on **accessible hardware**:

- **INT4 W4A16** quantization of the model (16.9 GiB on disk)
- **Tensor Parallel 2** splitting weights across two PCIe-linked GPUs
- **FP8 KV cache** allowing 234,570 cached tokens in 32 GB of VRAM
- Kernel tuning (`vm.overcommit_memory=1`) to allow `mmap` of the
  checkpoint on hosts with less RAM than the model size

The result is an OpenAI-compatible endpoint with two-layer authentication
and full audit, deployed entirely with Docker Compose and reproducible on
any environment with minimum hardware.

### Infrastructure status

| Period | Status |
|---|---|
| 2025–2026 | BF16 reference deployment under development and validation |
| November 2026 | Migration to INT4 with vLLM + LiteLLM + PostgreSQL |
| Current | TORQUEMADA INT4 operational · 65K context · 2× RTX 2000 Ada |

### What the guides cover

- ✅ vLLM deployment with `compressed-tensors` W4A16 checkpoint
- ✅ Tensor Parallel 2 across GPUs without NVLink
- ✅ FP8 KV cache with 3.58× concurrency at 65K tokens
- ✅ Kernel tuning (`vm.overcommit_memory`) for large-checkpoint `mmap`
- ✅ LiteLLM gateway with master key and internal `api_base`
- ✅ PostgreSQL 16 with LiteLLM's 232 Prisma migrations applied
- ✅ Two-layer authentication (client → LiteLLM → vLLM)
- ✅ UFW firewall with minimum exposure
- ✅ Complete backup procedure (compose, config, inspect, SQL dump)
- ✅ Failure diagnostics (`Cannot allocate memory`, NCCL, KV OOM)
- ✅ 5-test functional validation suite
- ✅ Hardware notes for mid-range GPU hosts

---

## 📚 Index of Documents / Índice de Documentos

### 🚀 vLLM Inference Engine

| Document | Description |
|---|---|
| [vLLM INT4 Deployment](docs/vllm-int4-deployment.md) | Full vLLM deployment procedure for `compressed-tensors` W4A16 |
| [Kernel Tuning for Large Checkpoints](docs/kernel-tuning-overcommit.md) | `vm.overcommit_memory` fix for `mmap` failures on large models |
| [Tensor Parallel on PCIe GPUs](docs/tensor-parallel-pcie.md) | NCCL tuning for GPUs without NVLink (RTX 2000 Ada, RTX 4000, etc.) |
| [FP8 KV Cache Sizing](docs/kv-cache-fp8-sizing.md) | Sizing the KV cache for 65K context on limited VRAM |

### 🌐 LiteLLM Gateway

| Document | Description |
|---|---|
| [LiteLLM + PostgreSQL Deployment](docs/litellm-postgresql-deployment.md) | Full gateway deployment with master key authentication |
| [Model Routing Configuration](docs/litellm-model-routing.md) | `config.yaml` structure for internal model routing |
| [Double Authentication Pattern](docs/litellm-double-auth.md) | Client → LiteLLM → vLLM authentication design |

### 💾 Data and Audit

| Document | Description |
|---|---|
| [PostgreSQL Persistence](docs/postgresql-persistence.md) | Data volume, migrations and backup strategy |
| [Audit Log Structure](docs/audit-log-structure.md) | `LiteLLM_SpendLogs` table and derived views |
| [Backup Procedure](docs/backup-procedure.md) | Full snapshot including compose, config and SQL dump |

### 🔒 Security and Operations

| Document | Description |
|---|---|
| [Firewall Configuration](docs/firewall-ufw.md) | UFW rules and Docker bypass considerations |
| [Docker Network Isolation](docs/docker-network-isolation.md) | Planned `torquemada-net` internal network (roadmap) |
| [Operational Runbook](docs/operational-runbook.md) | Daily commands, restart procedures, health checks |

### 📖 Reference

| Document | Description |
|---|---|
| [Architecture Overview](docs/architecture-overview.md) | Full stack diagram and component responsibilities |
| [Hardware Notes](docs/hardware-notes.md) | GPU selection, VRAM budgeting, PCIe considerations |
| [Final Deployment Report](docs/final-deployment-report.md) | Complete operational report with verification results |
| [Glossary](docs/glossary.md) | vLLM, LiteLLM, quantization and virtualization terms |

---

## 🚀 Where to Start

**🇪🇸 Si nunca has desplegado un LLM de gran tamaño en hardware modesto:**

1. Lee [Architecture Overview](docs/architecture-overview.md) — contexto del stack completo
2. Lee [Hardware Notes](docs/hardware-notes.md) — qué GPU necesitas y por qué
3. Sigue [vLLM INT4 Deployment](docs/vllm-int4-deployment.md) para el motor
4. Añade el gateway con [LiteLLM + PostgreSQL](docs/litellm-postgresql-deployment.md)
5. Consulta [Operational Runbook](docs/operational-runbook.md) para el día a día

**🇬🇧 If you have never deployed a large LLM on modest hardware:**

1. Read [Architecture Overview](docs/architecture-overview.md) — full stack context
2. Read [Hardware Notes](docs/hardware-notes.md) — which GPU you need and why
3. Follow [vLLM INT4 Deployment](docs/vllm-int4-deployment.md) for the engine
4. Add the gateway with [LiteLLM + PostgreSQL](docs/litellm-postgresql-deployment.md)
5. Consult [Operational Runbook](docs/operational-runbook.md) for day-to-day

---

## 🧰 Stack Summary

| Layer | Technology | Role |
|---|---|---|
| Model | [OLMo-3.1-32B-Think](https://huggingface.co/allenai/OLMo-3.1-32B-Think) | 32B-parameter LLM with explicit reasoning |
| Quantization | `compressed-tensors` W4A16 (INT4) | 75% size reduction with minimal quality loss |
| Inference engine | [vLLM 0.33.0](https://github.com/vllm-project/vllm) | High-throughput OpenAI-compatible server |
| Gateway | [LiteLLM](https://github.com/BerriAI/litellm) | Multi-model proxy with auth and logging |
| Database | [PostgreSQL 16](https://www.postgresql.org/) | Audit logs, configuration, user management |
| Orchestration | Docker Compose | Declarative, reproducible deployment |
| Host OS | Ubuntu 24.04 LTS | Tested operating system |

### Minimum requirements

- **2 GPUs** with ≥16 GB VRAM each (tested on 2× RTX 2000 Ada)
- **32 GB RAM**
- **30 GB free disk** (16.9 GB for the model alone)
- **Docker 24+** and **Docker Compose 2+**
- **NVIDIA driver 580+** with NVIDIA Container Toolkit

---

## 🤝 Contributing

We welcome contributions. See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

Especially valuable:

- Procedure corrections based on your hardware environment
- Coverage of alternative inference engines (SGLang, TGI, llama.cpp)
- Adaptation to other quantizations (AWQ, EXL2, GGUF)
- Bug reports on procedures that did not work for you
- Translations to other languages

---

## 📖 Glossary

Acronyms and product-specific terms are defined in the
[Glossary](docs/glossary.md). When in doubt, check there before opening
an issue.

---

## ⚖️ License

Apache License 2.0 — see [LICENSE](LICENSE).

You are free to use these procedures commercially, modify them,
redistribute them and incorporate them into your own documentation.
Attribution is appreciated but not required.

**No warranty.** These procedures were operated in our specific
environment. Model behavior, hardware compatibility and software
versions evolve quickly. Always test in a development environment
before applying to production.

---

## 🏛️ About TOWIN-IA

**TOWIN-IA** is the sovereign AI infrastructure project of **TYBSI SL**,
based in **Zamora, Spain**. We build on-premise AI compute capacity for
European clients under European jurisdiction, with European-origin models
running on hardware we control.

Our infrastructure is operated under principles of **digital
sovereignty**: hardware in our datacenter, models from European and
aligned vendors (Mistral, OLMo), data that never leaves our racks, full
compliance with EU regulations (GDPR, NIS2, AI Act).

This repository is one expression of our commitment to giving back to
the technical community that makes our work possible.

🌐 [towinia.es](https://towinia.es) · ✉️ onpremise@towinia.es

---

## 📅 Repository History

| Version | Date | Notes |
|---|---|---|
| v1.0 | November 2026 | Initial public release · INT4 deployment operational |

---

## 🌟 Acknowledgments

To the operators, developers, and technical communities who make
on-premise LLM inference possible:

- The **Allen Institute for AI (Ai2)** for the OLMo open-weights family
- The **vLLM Project** for a production-grade inference engine
- **BerriAI** for LiteLLM and its OpenAI-compatible abstraction
- The **PostgreSQL Global Development Group**
- The **NVIDIA CUDA and NCCL** engineering teams
- The **r/LocalLLaMA** and **r/homelab** communities for practical GPU wisdom
- The students from **IES Lliçà**, **CECAC Santa Coloma**, and
  **Salesianos Cádiz** who built and operated our GPU infrastructure
  alongside us

---

**Built with rigor in Zamora, Spain.**  
**Released to the community.**
