# 🔐 Laboratórios de Hash e Criptografia — FIAP

Bem-vindo! Este repositório contém **2 desafios práticos** sobre criptografia moderna: funções de hash, criptografia simétrica e assimétrica.

---

## 🎯 Estrutura do Repositório

```
├── Desafio-01/  → Fundamentos Teóricos
├── Desafio-02/  → Prática com Docker (CTF)
└── README.md    → Este arquivo
```

---

## 📚 Desafio 01 — Fundamentos de Hash e Criptografia

**Objetivo:** Entender os três pilares da criptografia moderna.

**Duração:** ~45 minutos

**Tópicos:**
- ✅ Funções de Hash (MD5, SHA-256)
- ✅ Criptografia Simétrica (AES)
- ✅ Criptografia Assimétrica (RSA)
- ✅ Ataques práticos (rainbow tables, força bruta, fatoração)

### 📖 Como Começar

```bash
cd Desafio-01/
cat README.md
```

**Conteúdo:**
- **README.md** — Visão geral e objetivos
- **PARTE0_SETUP.md** — Instalação e ambiente (se necessário)
- **PARTE1_LEARN.md** — Conceitos teóricos
- **PARTE2_APPLY.md** — Exemplos práticos com OpenSSL
- **PARTE3_CHALLENGE.md** — Desafios para resolver
- **GUIA_RAPIDO.md** — Referência rápida de comandos

---

## 🐳 Desafio 02 — CTF Prático com Docker

**Objetivo:** Capturar FLAGS resolvendo 5 desafios de criptografia.

**Duração:** ~60 minutos (5 desafios progressivos)

**Estrutura:**
1. **Easy (10 min):** Hash MD5 → Rainbow tables
2. **Medium (10 min):** Hash SHA-256 + Salt → Força bruta
3. **Hard (15 min):** Criptografia Simétrica (AES-256) → Quebra de chave
4. **Expert (15 min):** Criptografia Assimétrica (RSA) → Fatoração
5. **Master (10 min):** Criar sistema de encriptação

### 🚀 Como Começar

```bash
cd Desafio-02/
docker-compose up
# Abra navegador: http://localhost:8080
```

**Conteúdo:**
- **README.md** — Guia do CTF
- **Dockerfile** — Imagem com ferramentas (openssl, hashcat, gnupg)
- **docker-compose.yml** — Orquestração
- **challenges/** — Arquivos dos desafios
- **index.html** — Interface web

---

## 🛠️ Ferramentas Incluídas

| Ferramenta | Desafio | Uso |
|-----------|---------|-----|
| **OpenSSL** | 1, 2, 3, 4 | Criptografia, hashing |
| **Hashcat** | 1, 2 | Quebra de hash (GPU) |
| **John the Ripper** | 1, 2 | Quebra de senha (CPU) |
| **GnuPG** | 4, 5 | Criptografia assimétrica |
| **Python (pycryptodome)** | 5 | Implementar criptografia |

---

## 🎓 Fluxo Recomendado

### Para Alunos Novatos

```
1. Fazer Desafio-01 (teoria + exemplos)
   ↓
2. Depois, Desafio-02 (prática CTF)
   ↓
3. Opcional: Criar seu próprio sistema de encriptação
```

### Para Quem tem Experiência

```
1. Fazer Desafio-02 direto (CTF intensivo)
   ↓
2. Opcionalmente, revisar Desafio-01 para conceitos
```

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

### Desafio-01
| Problema | Solução |
|----------|---------|
| OpenSSL não instalado | `apt install openssl` (Linux) ou use Docker |
| Comando não encontrado | Verifique a sintaxe exata |

### Desafio-02
| Problema | Solução |
|----------|---------|
| Docker não inicia | Abra Docker Desktop |
| Port 8080 ocupada | Use `docker-compose -p 8081:80 up` |
| Página branca | Aguarde 5 segundos ou recarregue |

---

## 🔗 Recursos Externos

- **OpenSSL Manual:** https://www.openssl.org/docs/
- **Hashcat:** https://hashcat.net/wiki/
- **NIST Guidelines:** https://csrc.nist.gov/publications/detail/sp/800-175b/final
- **Cryptopals Challenges:** https://cryptopals.com/

---

## 📝 Licença

MIT — Desenvolvido para FIAP — Cybersecurity Hacker Mindset | 2026

---

## 📞 Começar Agora!

```bash
# Clone este repositório
git clone https://github.com/juliocataldo/crypto-lab.git
cd crypto-lab

# Escolha seu caminho
cd Desafio-01/   # Teória
# ou
cd Desafio-02/   # Prática CTF

# Siga o README de cada desafio
cat README.md
```

**Bom desafio! 🚀**
