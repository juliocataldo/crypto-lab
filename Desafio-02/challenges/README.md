# 📁 Arquivos de Desafio — Cripto Lab

Esta pasta contém os arquivos necessários para os desafios do Cripto Lab.

## 🔧 Gerar Arquivos de Desafio

Antes de rodar o Docker, execute:

```bash
python3 gera_desafios.py
```

Isso irá (re)gerar, com valores reais e verificados:
- `hash_md5.txt` — Hash MD5 para quebrar
- `hash_sha256_salt.txt` — Hash SHA-256 com salt
- `criptografado.bin` — Arquivo encriptado com AES-256-CBC
- `numeros_rsa.txt` — Parâmetros públicos do RSA pequeno (N, e)
- `mensagem_encriptada_rsa.txt` — Mensagem cifrada bloco-a-bloco com RSA
- `chave_publica.pem` — Chave pública RSA (SubjectPublicKeyInfo/DER)
- `wordlist_comum.txt` — Wordlist local para os Desafios 1 e 2 (100% offline, sem internet)
- `RESPOSTAS.md` — Gabarito de uso interno (gitignored, não é publicado)

O script (`gera_desafios.py`) também é ignorado pelo git (`gera_*.py` no `.gitignore`) — fica só na máquina de quem monta o lab.

## 📥 Arquivos Disponíveis para Download

Quando o lab estiver rodando em http://localhost:8080, todos os arquivos aqui (exceto `gera_desafios.py` e `RESPOSTAS.md`) estarão disponíveis para download.

## 🎯 Desafios

| Arquivo | Desafio | Tipo |
|---------|---------|------|
| `hash_md5.txt` | Quebre MD5 | Hash Easy |
| `hash_sha256_salt.txt` | Quebre SHA256+salt | Hash Medium |
| `criptografado.bin` | Descriptografe AES-256 | Criptografia Hard |
| `numeros_rsa.txt` + `mensagem_encriptada_rsa.txt` + `chave_publica.pem` | Fatore RSA e decifre | Assimétrica Expert |

---

**Desenvolvido para FIAP Cybersecurity Hacker Mindset | 2026**
