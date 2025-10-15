# Autogen Çoklu LLM Örnekleri

Bu repo Microsoft AutoGen ekosistemi (autogen-ext + autogen-agentchat) ile farklı sağlayıcılara (OpenAI, Gemini, OpenRouter, Ollama, Anthropic, Semantic Kernel adaptörü) bağlanan Jupyter notebook örnekleri içerir.

## Dizindeki Notebooklar

- 1- OpenAIAutogen.ipynb: OpenAI ChatCompletionClient ile temel tek ajan kurulumu ve basit soru yanıtlama akışı.
- 2- GeminiAutogen.ipynb: Aynı AutoGen arayüzünü kullanarak Gemini (OpenAI uyumlu endpoint) modeliyle yanıt üretimi.
- 3- OpenRouterAutogen.ipynb: OpenRouter gateway üzerinden çoklu model seçimi ve özel base_url/header yapılandırması örneği.
- 4- OllamaAutogen.ipynb: Lokal Ollama sunucusuna bağlanıp indirilen open‑source modeli (örn. llama3) ile etkileşim.
- 5- AnthropicAutogen.ipynb: Anthropic Claude modeline istek gönderme ve mesaj geçmişi yönetimi gösterimi.
- 6- SemanticKernelAdapter.ipynb: Semantic Kernel chat completion adaptörünü AutoGen ajan zincirine entegre etme örneği.

Bağımlılıklar: `requirements.txt`

## Kurulum

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# (İsteğe bağlı) Kernel
pip install ipykernel
python -m ipykernel install --user --name autogen-env --display-name "autogen-env"
pip install -r requirements.txt
```

## Ortam Değişkenleri

Gerçek anahtarları `.env` dosyasına koy (commit etme). Örnek şablon için `.env.example` oluşturduk.

```
# .env.example
OPENAI_API_KEY=your-openai-key
GEMINI_API_KEY=your-gemini-key
OPENROUTER_API_KEY=your-openrouter-key
ANTHROPIC_API_KEY=your-anthropic-key
```

Kullanım (her notebook başında):
```python
from dotenv import load_dotenv
load_dotenv()
```

## Güvenlik & .gitignore

`.env` artık `.gitignore` içine alındı. Eğer `.env` daha önce commitlendiyse:

```bash
git rm --cached .env
git commit -m "Remove .env from repo"
git push
```

Geçmişten tamamen silmek istiyorsan (opsiyonel, güçlü adım):

(BFG örneği)
```bash
# BFG kur -> https://rtyley.github.io/bfg-repo-cleaner/
java -jar bfg.jar --delete-files .env
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force
```

Ardından servis panellerinden (OpenAI, Google AI Studio, OpenRouter, Anthropic) anahtarları ROTATE et.

## Sağlayıcı Özetleri

- OpenAI: `OpenAIChatCompletionClient`
- Gemini: OpenAI uyumlu endpoint modeli (örn. `gemini-2.5-flash`)
- OpenRouter: `base_url` ayarlı, farklı modelleri köprüler
- Ollama: Lokal model (örn. `ollama pull llama3.2`)
- Anthropic: `AnthropicChatCompletionClient`
- Semantic Kernel Adaptörü: `SKChatCompletionAdapter`

## Minimal Örnek

```python
from dotenv import load_dotenv
import os, nest_asyncio
nest_asyncio.apply()

from autogen_ext.models.openai import OpenAIChatCompletionClient
from autogen_agentchat.agents import AssistantAgent

load_dotenv()

client = OpenAIChatCompletionClient(model="gpt-4o")
agent = AssistantAgent(
    name="helper",
    model_client=client,
    system_message="You are a helpful assistant."
)

resp = await agent.run(task="Fransa'nın başkenti nedir?")
print(resp.messages[-1].content)

await client.close()
```


## Genişletme Fikirleri

- Multi-agent (UserProxy + Assistant)
- Fonksiyon çağırma / tool calling
- Yapılandırılmış JSON çıktı
- Diyalog geçmişi persistance (SQLite / dosya)
- Otomatik değerlendirme (self-critique)

## Script Ortamından Çalıştırma

```bash
python - << "PY"
from dotenv import load_dotenv; load_dotenv()
from autogen_ext.models.openai import OpenAIChatCompletionClient
from autogen_agentchat.agents import AssistantAgent
import asyncio

async def main():
    client = OpenAIChatCompletionClient(model="gpt-4o")
    agent = AssistantAgent(name="helper", model_client=client)
    r = await agent.run(task="Say hi briefly.")
    print(r.messages[-1].content)
    await client.close()

asyncio.run(main())
PY
```

## Güvenlik İpuçları

- Anahtarları CI / CD’de Secret Store’dan enjekte et.
- Paylaşılan ekran kayıtlarında terminali maskeye al.
- Rate-limit & kota takibi yap.

## Lisans

Uygun bir lisans seç (MIT / Apache-2.0) ve `LICENSE` ekle.

---

Kısa Özet: Bu repo AutoGen ile çoklu LLM sağlayıcı örnekleri sunar; `.env` gizlenmiş, anahtar yönetimine dikkat edilmelidir.