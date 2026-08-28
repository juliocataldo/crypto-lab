# 🔐 Cripto Lab — FIAP

Bem-vindo! Laboratório prático de criptografia moderna: funções de hash, criptografia simétrica e assimétrica, em formato CTF.

---

## 🎯 Estrutura do Repositório

```
├── Desafio-02/  → CTF prático com Docker
└── README.md    → Este arquivo
```

---

## 🐳 O Desafio — CTF Prático com Docker

**Objetivo:** Capturar FLAGS resolvendo 5 desafios de criptografia.

**Duração:** ~60 minutos (5 desafios progressivos)

**Estrutura:**
1. **Easy (10 min):** Hash MD5 → John the Ripper + wordlist (offline)
2. **Medium (10 min):** Hash SHA-256 + Salt → Força bruta
3. **Hard (15 min):** Criptografia Simétrica (AES-256) → Quebra de chave
4. **Expert (15 min):** Criptografia Assimétrica (RSA) → Fatoração
5. **Master (10 min):** Criar sistema de encriptação (checklist autoavaliado)

### 🚀 Como Começar

```bash
cd Desafio-02/
docker compose up -d --build
# Abra navegador: http://localhost:8080
```

**Conteúdo:**
- **README.md** — Guia do CTF
- **Dockerfile** — Imagem com ferramentas (openssl, john, python3+sympy, gnupg)
- **docker-compose.yml** — Orquestração
- **challenges/** — Arquivos dos desafios (gerados por `gera_desafios.py`)
- **index.html** — Interface web (tema CTF, flags validadas no navegador)
- **COMANDOS.md** — Dicionário rápido de comandos (docker, ferramentas, paths dentro do container)

> Requer só Docker — depois do build, os 5 desafios rodam 100% offline (sem internet). Docker é necessário porque as ferramentas (openssl, john, sympy) não existem nativamente no Windows.

---

## 🛠️ Ferramentas Incluídas

| Ferramenta | Desafio | Uso |
|-----------|---------|-----|
| **OpenSSL** | 3, 5 | Criptografia simétrica/assimétrica, hashing |
| **John the Ripper** | 1, 2 | Quebra de hash/senha (CPU) — com wordlist embutida, sem internet |
| **Python (sympy)** | 4 | Fatoração do RSA pequeno |
| **Python (pycryptodome)** | 5 | Implementar criptografia própria |
| **GnuPG** | 5 (opcional) | Criptografia assimétrica alternativa |

> `hashcat` não está incluído (sem pacote para Alpine/musl) — `john` cobre os mesmos desafios.

---

## 📋 Conceitos-Chave

### Hash vs Criptografia

| Aspecto | Hash | Criptografia |
|---------|------|--------------|
| **Reversível?** | ❌ Não | ✅ Sim |
| **Uso** | Integridade, senhas | Confidencialidade |
| **Output** | Sempre mesmo tamanho | Varia com input |
| **Exemplo** | SHA-256 | AES, RSA |

### Simétrica vs Assimétrica

| Aspecto | Simétrica | Assimétrica |
|---------|-----------|------------|
| **Chaves** | 1 chave compartilhada | Par público/privado |
| **Velocidade** | ⚡ Rápida | 🐢 Lenta |
| **Problema** | Como compartilhar chave? | Difícil gerar chaves grandes |
| **Exemplo** | AES, DES | RSA, ECC |

---

## ⚠️ Troubleshooting Rápido

| Problema | Solução |
|----------|---------|
| Docker não inicia | Abra Docker Desktop |
| Port 8080 ocupada | Edite `ports: ["8081:80"]` em `docker-compose.yml` (não é `docker-compose -p`, que é `--project-name`, não porta) |
| Página branca | Aguarde alguns segundos e recarregue |
| Comando dentro do container falha com "Read-only file system" | `challenges/` é montado somente leitura — copie os arquivos para `/tmp` antes de trabalhar |

Veja mais detalhes em [Desafio-02/README.md](Desafio-02/README.md).

---

## 🔗 Recursos Externos

- **OpenSSL Manual:** https://www.openssl.org/docs/
- **John the Ripper:** https://www.openwall.com/john/
- **NIST Guidelines:** https://csrc.nist.gov/publications/detail/sp/800-175b/final
- **Cryptopals Challenges:** https://cryptopals.com/

---

## 📝 Licença

MIT — Desenvolvido para FIAP — Cybersecurity Hacker Mindset | 2026

---

## 📞 Começar Agora!

```bash
cd Desafio-02/
cat README.md
docker compose up -d --build
```

**Bom desafio! 🚀**
