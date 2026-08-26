# 📁 Arquivos de Desafio — Cripto Lab

Esta pasta contém os arquivos necessários para os desafios do Cripto Lab.

## 🔧 Gerar Arquivos de Desafio

Antes de rodar o Docker, execute:

```bash
python3 gera_desafios.py
```

Isso irá gerar:
- `hash_md5_valor.txt` — Hash MD5 para quebrar
- `hash_sha256_valor.txt` — Hash SHA256 com salt
- `criptografado.bin` — Arquivo encriptado com AES-256
- `numeros_rsa_valores.txt` — Parâmetros do RSA pequeno
- `chave_publica.pem` — Chave pública RSA
- `mensagem_encriptada_rsa.txt` — Mensagem encriptada

## 📥 Arquivos Disponíveis para Download

Quando o lab estiver rodando em http://localhost:8080, todos os arquivos aqui estarão disponíveis para download.

## 🎯 Desafios

| Arquivo | Desafio | Tipo |
|---------|---------|------|
| `hash_md5.txt` | Quebre MD5 | Hash Easy |
| `hash_sha256_salt.txt` | Quebre SHA256+salt | Hash Medium |
| `criptografado.bin` | Descriptografe AES-256 | Criptografia Hard |
| `numeros_rsa.txt` | Fatore RSA | Assimétrica Expert |

---

**Desenvolvido para FIAP Cybersecurity Hacker Mindset | 2026**
