# 📘 Atividade 1 — Sistemas Operacionais
## IA Generativa Local com Ollama + Open WebUI

**Discente:** Augusto César Honorato dos Santos  
**Disciplina:** Sistemas Operacionais — UFS 2026.2  
**Trilha:** A — Chat local: Ollama + Open WebUI  
**Modelo:** `llama3.2:1b` (1.2B parâmetros, GGUF Q4_0)  
**Data de entrega:** 15/09/2026

---

## 🎯 Sobre a atividade

Este repositório reúne os artefatos da Atividade 1 da disciplina de Sistemas Operacionais. O objetivo é instalar, executar e **observar** uma aplicação local de IA generativa baseada em Ollama, medindo o impacto de diferentes configurações sobre processos, threads, CPU, memória e chamadas de sistema.

**Pergunta norteadora:** como a camada de aplicação, o modelo, a quantização e a configuração de execução afetam processos, threads, uso de CPU, memória e responsividade de um sistema local de IA generativa?

---

## 🧱 Arquitetura

```
┌──────────────────────────┐
│     Navegador (host)     │
│  http://10.0.2.15:3000   │
└────────────┬─────────────┘
             │ HTTP
             ▼
┌──────────────────────────┐
│  Open WebUI (container)  │
│  porta 8080 interna      │
└────────────┬─────────────┘
             │ HTTP via host.docker.internal:11434
             ▼
┌──────────────────────────┐
│   Ollama (systemd)       │
│   porta 11434            │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│  llama3.2:1b (GGUF Q4_0) │
│  1.3 GB em disco         │
└──────────────────────────┘
```

---

## 🖥️ Ambiente experimental

| Item | Valor |
|------|-------|
| Sistema Operacional | Ubuntu 24.04.2 LTS |
| Ambiente | Máquina Virtual (VirtualBox) |
| RAM total | 3.916 MB (~3,8 GB) |
| Swap | 0 MB |
| Disco `/` | 91,75 GB (25,1% usado) |
| Ollama | 0.34.0 |
| Docker | 29.1.3 |
| Open WebUI | `ghcr.io/open-webui/open-webui:main` |

---

## 📦 Modelo utilizado

| Item | Valor |
|------|-------|
| Nome | `llama3.2:1b` |
| Parâmetros | 1,2 bilhões |
| Formato | GGUF |
| Quantização | Q4_0 |
| Tamanho em disco | 1,3 GB |
| Contexto máximo | 131.072 tokens |
| Licença | Llama 3.2 Community License |

---

## ⚙️ Instalação e execução

### 1. Instalar o Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### 2. Permitir acesso externo ao Ollama

```bash
sudo systemctl edit ollama
# Adicione o conteúdo abaixo no editor:
# [Service]
# Environment="OLLAMA_HOST=0.0.0.0"

sudo systemctl daemon-reload
sudo systemctl restart ollama
```

### 3. Baixar o modelo

```bash
ollama pull llama3.2:1b
```

### 4. Subir o Open WebUI em container

```bash
sudo docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  -e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
  ghcr.io/open-webui/open-webui:main
```

### 5. Acessar no navegador

```
http://10.0.2.15:3000
```

---

## 🧪 Experimentos realizados

Foram realizadas **12 execuções mensuráveis**, divididas em 3 configurações.

### Configuração 1 — Entradas longas

| # | Prompt | Tempo real |
|---|--------|------------|
| 1 | Explicar processo em SO | 3m54,6s |
| 2 | Explicar thread | 3m41,5s |
| 3 | Escalonamento de CPU | 2m19,9s |
| 4 | Memória virtual (300 palavras) | 3m00,0s |

### Configuração 2 — Entradas curtas

| # | Prompt | Tempo real |
|---|--------|------------|
| 5 | "Oi" | 2,0s |
| 6 | "Tudo bem?" | 7,5s |
| 7 | "Capital do Brasil" | 3,2s |
| 8 | "Diga apenas: ok" | 9,3s |

### Configuração 3 — Concorrência (2 prompts simultâneos)

| # | Prompts | Tempo real |
|---|---------|------------|
| 9 | Threads + Processos | 17,3s |
| 10 | Memória + CPU | 6m17,9s |
| 11 | Deadlock + Semáforo | 1m26,7s |
| 12 | Paginação + Swapping | 1m46,7s |

---

## 📊 Resultados principais

- **CPU do `llama-server` (pico):** 196,2% (~2 núcleos)
- **Memória do `llama-server` (pico):** 1,4 GB residentes (37,4%)
- **RAM livre durante inferência:** 102–124 MB
- **Load average (pico):** 1,81 / 1,15 / 2,23

### Chamadas de sistema mais relevantes (`strace`)

| Syscall | % tempo | Papel |
|---------|---------|-------|
| `futex` | 49,38% | Sincronização entre threads |
| `nanosleep` | 17,93% | Pausas curtas de threads |
| `epoll_pwait` | 17,75% | Espera de eventos de I/O |
| `write` | 2,16% | Escrita da resposta HTTP |
| `read` | 1,71% | Leitura de dados e rede |

---

## ⚠️ Limitações observadas

1. RAM muito baixa (3,8 GB) e ausência de GPU dedicada.
2. Ambiente virtualizado, com possível competição por recursos do host.
3. Modelo pequeno (1,2B) apresentou **confabulações** em vários prompts, reforçando a necessidade de verificação crítica de respostas geradas por IA.
4. Alta variância nos tempos medidos, o que dificulta comparações diretas entre execuções.

---

## 🧠 Uso crítico de IA generativa

Ferramentas de IA foram usadas como apoio para interpretação de comandos, estruturação do relatório e análise dos resultados. Todos os dados numéricos foram obtidos diretamente dos comandos `time`, `top` e `strace`, e conferidos nos logs brutos disponíveis em `logs/todos_logs.txt`.

---

## 📚 Referências

- SILBERSCHATZ, A.; GALVIN, P.; GAGNE, G. *Operating System Concepts*. 10. ed. Wiley, 2018.
- TANENBAUM, A.; BOS, H. *Modern Operating Systems*. 5. ed. Pearson, 2023.
- KERRISK, M. *The Linux Programming Interface*. No Starch Press, 2010.
- [Ollama](https://github.com/ollama/ollama)
- [Open WebUI](https://github.com/open-webui/open-webui)
- [Hugging Face Models](https://huggingface.co/models)

---

## 👤 Autor

**Augusto César Honorato dos Santos**  
Disciplina: Sistemas Operacionais — UFS 2026.2  
Repositório: [SO_UFS_2026_2_Honorato_Augusto_Cesar](https://github.com/ACesarhs/SO_UFS_2026_2_Honorato_Augusto_Cesar)
