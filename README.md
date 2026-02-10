# 🎙️ Reconhecimento de Voz + ChatGPT + Resposta em Áudio (Google Colab)

Este projeto grava sua voz no **Google Colab**, transcreve o áudio usando **Whisper**, envia o texto para o **ChatGPT (OpenAI API)** e, por fim, gera uma **resposta falada** usando **gTTS**.

> ⚠️ Observação: o script foi feito para rodar no **Google Colab** (usa `google.colab.output` e `Javascript` para capturar áudio via navegador).

---

## ✅ Fluxo do projeto

1. **Grava áudio do microfone** (JavaScript no Colab)
2. **Salva em WAV** (`request_audio.wav`)
3. **Transcreve** o áudio com `whisper`
4. Envia a transcrição para **ChatGPT**
5. Converte a resposta em **áudio** com `gTTS`
6. Reproduz o áudio final no Colab

---

## 📦 Dependências

No Colab, instale:

```bash
!pip install -U openai whisper gTTS
````

> O Whisper pode exigir ffmpeg dependendo do ambiente, mas no Colab normalmente já funciona.

---

## 🔐 Configurando a OpenAI API Key (do jeito certo)

Nunca coloque sua chave no código. Use variável de ambiente:

```python
import os
os.environ["OPENAI_API_KEY"] = "SUA_CHAVE_AQUI"
```

Ou (melhor) use o **Secrets** do Colab / variáveis no ambiente.

---

## ▶️ Como usar

1. Rode a célula
2. Permita acesso ao microfone
3. Aguarde a gravação (padrão: **5 segundos**)
4. Veja a transcrição
5. Receba a resposta em texto e áudio

---

## 🧠 O que o código faz (explicação por blocos)

### 1) Captura de áudio no Colab (JavaScript)

O trecho `RECORD` cria um gravador usando `MediaRecorder`, captura o áudio do microfone e devolve um **base64**.

A função `record(sec=5)`:

* executa o JS
* decodifica o base64
* salva como `request_audio.wav`
* retorna o caminho do arquivo

---

### 2) Transcrição com Whisper

Carrega o modelo e transcreve:

```python
import whisper
model = whisper.load_model("small")

result = model.transcribe(record_file, fp16=False, language=language)
transcription = result["text"]
```

---

### 3) Envio para o ChatGPT (OpenAI API)

Cria um cliente com a API Key no ambiente e manda a transcrição como mensagem do usuário:

```python
from openai import OpenAI
client = OpenAI(api_key=os.environ["OPENAI_API_KEY"])

response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": transcription}]
)
```

---

### 4) Texto → Áudio com gTTS

Converte a resposta do ChatGPT em áudio e toca no Colab:

```python
from gtts import gTTS
gtts_object = gTTS(text=chatgpt_response, lang=language, slow=False)
```

---

## 🛠️ Correções necessárias no seu código (importante)

No final, seu script usa `chatgpt_response`, mas essa variável **não foi definida**.

✅ Ajuste recomendado:

```python
chatgpt_response = response.choices[0].message.content
print(chatgpt_response)
```

E só depois gerar o áudio:

```python
gtts_object = gTTS(text=chatgpt_response, lang=language, slow=False)
```

---

## 💡 Sugestões de melhorias

* Permitir escolher duração da gravação (`record(10)`, por exemplo)
* Tratar erros (sem permissão de microfone, falha na API, etc.)
* Trocar modelo do Whisper (`base`, `medium`, etc.)
* Adicionar “prompt de sistema” para definir o comportamento do assistente
* Salvar histórico de conversas (para contexto)

---

## 📁 Estrutura gerada

Durante a execução, os arquivos são criados em:

* `request_audio.wav` → áudio gravado
* `response_audio.wav` → áudio com a resposta

---

## 📜 Licença

Escolha uma licença para seu repositório (ex.: MIT).

---

## ▶️ Executar no Google Colab

Você pode testar o projeto diretamente no Google Colab, sem precisar instalar nada localmente:

[![Abrir no Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1pQMCV-eFvuL5CfU-O21slfs6cUyZXAM2?usp=sharing)

Ou acesse pelo link:
https://colab.research.google.com/drive/1pQMCV-eFvuL5CfU-O21slfs6cUyZXAM2?usp=sharing

---

## ✨ Créditos

* [OpenAI Whisper](https://github.com/openai/whisper)
* [OpenAI API](https://platform.openai.com/)
* [gTTS](https://pypi.org/project/gTTS/)

---
