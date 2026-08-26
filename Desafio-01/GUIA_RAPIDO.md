# 🔐 CRIPTO LAB — Guia Rápido (60 min)

## ✅ Pré-requisitos
- Docker Desktop instalado
- Terminal (cmd, PowerShell ou Bash)

## 🚀 Iniciar

```bash
# Comando único (copia e cola)
docker run -it -p 8080:80 --rm jaycewilliam/cripto-lab:latest
```

Aguarde aparecer:
```
[+] Cripto Lab iniciado em http://localhost:8080
```

## 🌐 Acessar

Abra no navegador:
```
http://localhost:8080
```

## 🎯 Objetivos (60 min)

| Tempo | Desafio | Tipo | Ação |
|-------|---------|------|------|
| 5 min | Ler página | Intro | Baixe todos os 5 arquivos |
| 10 min | Easy | Hash MD5 | Rainbow table / hashcat `-m 0` |
| 10 min | Medium | Hash SHA256 + Salt | Força bruta com dicionário |
| 15 min | Hard | AES-256-CBC | Descriptografe com OpenSSL |
| 15 min | Expert | RSA (pequeno) | Fatore e reconstrua chave privada |
| 10 min | Master | Hybrid | Crie sistema com 3 camadas |

## 💡 Dicas Rápidas

### Easy: MD5
```bash
# Hash MD5 é rapidíssimo de quebrar
hashcat -m 0 hashes.txt /usr/share/dict/rockyou.txt
# Ou online: https://md5.gromweb.com/
```

### Medium: SHA256 + Salt
```bash
# Reconhecer o salt
cat hash_sha256_salt.txt

# Criar hashes de teste
echo -n "abc123xyzsenha1" | sha256sum

# Comparar com hash esperado
```

### Hard: AES-256
```bash
# Descriptografar com chave conhecida
openssl enc -aes-256-cbc -d -in criptografado.bin \
  -K $(echo -n "minha_chave_secreta_32_bytes_ok" | xxd -p) -iv 0
```

### Expert: RSA
```bash
# 1. Ver números
cat numeros_rsa.txt

# 2. Fatorar N (pequeno para lab)
# N = p * q

# 3. Gerar chave privada
python3 gera_chave_privada.py --N <N> --e 65537

# 4. Descriptografar
openssl rsautl -decrypt -in mensagem_encriptada.txt -inkey chave_privada.pem
```

### Master: Hybrid (3 camadas)
```bash
# 1. Gerar chaves
openssl genrsa -out minha_chave_privada.pem 2048
openssl rsa -in minha_chave_privada.pem -pubout > minha_chave_publica.pem
openssl rand -hex 32 > chave_aes.hex

# 2. Hash + Encriptar
sha256sum mensagem.txt > hash_original.txt
openssl enc -aes-256-cbc -in mensagem.txt -out mensagem.aes \
  -K $(cat chave_aes.hex) -iv 0

# 3. Encriptar chave com RSA
openssl rsautl -encrypt -inkey minha_chave_publica.pem -pubin \
  -in chave_aes.hex -out chave_aes.enc

# 4. Compartilhar: mensagem.aes + chave_aes.enc + minha_chave_publica.pem
```

## 📊 FLAGS Esperadas

1. `FLAG{hash_md5_descoberto_hello}`
2. `FLAG{sha256_com_salt_descoberto_senha1}`
3. `FLAG{aes256_quebrada_chave_revelada_mensagem_confidencial}`
4. `FLAG{rsa_fatorado_chave_privada_reconstruida_segredo_revelado}`
5. `FLAG{sistema_de_encriptacao_em_tres_camadas_implementado_com_sucesso}`

## ❌ Problemas?

| Erro | Solução |
|------|---------|
| Port 8080 in use | Mude: `docker run -p 8081:80 ...` |
| Connection refused | Container ainda iniciando, aguarde 10 seg |
| openssl not found | Você está rodando dentro do container? |
| hashcat slow on CPU | Use `john` ou aceite que hashcat é GPU-focused |
| Python not found | Lab fornece python3 instalado |

## 🔑 Chaves de Teste

| Desafio | Plaintext/Chave |
|---------|---|
| Easy | `hello` |
| Medium | `senha1` |
| Hard | `minha_chave_secreta_32_bytes_ok` |
| Expert | Fatore N para encontrar p e q |
| Master | Você define! |

---

**Tempo limite: 60 minutos | Boa sorte! 🎯**
