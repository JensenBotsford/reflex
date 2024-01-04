```diff
Pynecone की तलाश हैं? आप सही रेपो में हैं। Pynecone का नाम Reflex में बदल दिया गया है। + +
```
<div align="center">
<img src="https://raw.githubusercontent.com/reflex-dev/reflex/main/docs/images/reflex_dark.svg#gh-light-mode-only" alt="Reflex Logo" width="300px">
<img src="https://raw.githubusercontent.com/reflex-dev/reflex/main/docs/images/reflex_light.svg#gh-dark-mode-only" alt="Reflex Logo" width="300px">

<hr>

### **✨ Python (पायथन) में परफॉर्मेंट, अनुकूलनयोग्य वेब ऐप्स। कुछ सेकंड्स में ही डिप्लॉय करें ✨**
[![PyPI version](https://badge.fury.io/py/reflex.svg)](https://badge.fury.io/py/reflex)
![tests](https://github.com/pynecone-io/pynecone/actions/workflows/integration.yml/badge.svg)
![versions](https://img.shields.io/pypi/pyversions/reflex.svg)
[![Documentaiton](https://img.shields.io/badge/Documentation%20-Introduction%20-%20%23007ec6)](https://reflex.dev/docs/getting-started/introduction)
[![Discord](https://img.shields.io/discord/1029853095527727165?color=%237289da&label=Discord)](https://discord.gg/T5WSbC2YtQ)
</div>

---
[English](https://github.com/reflex-dev/reflex/blob/main/README.md) | [简体中文](https://github.com/reflex-dev/reflex/blob/main/docs/zh/zh_cn/README.md) | [繁體中文](https://github.com/reflex-dev/reflex/blob/main/docs/zh/zh_tw/README.md) | [Türkçe](https://github.com/reflex-dev/reflex/blob/main/docs/tr/README.md) | [हिंदी](https://github.com/reflex-dev/reflex/blob/main/docs/in/README.md) | [한국어](https://github.com/reflex-dev/reflex/blob/main/docs/kr/README.md)
---

## ⚙️ इंस्टॉलेशन 

एक टर्मिनल खोलें और चलाएं (Python 3.8+ की आवश्यकता है):

```bash
pip install reflex
```

## 🥳 अपना पहला ऐप बनाएं

reflex को इंस्टॉल करने से ही reflex कमांड लाइन टूल भी इंस्टॉल हो जाता है।

सुनिश्चित करें कि इंस्टॉलेशन सफल थी, एक नया प्रोजेक्ट बनाकर इसे टेस्ट करें। ('my_app_name' की जगह अपने प्रोजेक्ट का नाम रखें): 

```bash
mkdir my_app_name
cd my_app_name
reflex init
```

यह कमांड आपकी नयी डायरेक्टरी में एक टेम्पलेट ऐप को प्रारंभ करता है।

आप इस ऐप को development मोड में चला सकते हैं:

```bash
reflex run
```

आपको http://localhost:3000 पर अपने ऐप को चलते हुए देखना चाहिए।

अब आप my_app_name/my_app_name.py में source कोड को संशोधित कर सकते हैं। Reflex में तेज रिफ्रेश की सुविधा है, इसलिए जब आप अपनी कोड को सहेजते हैं, तो आप अपने बदलावों को तुरंत देख सकते हैं।

## 🫧 उदाहरण ऐप

एक उदाहरण पर चलते हैं: DALL·E से एक इमेज उत्पन्न करने के लिए UI। सरलता के लिए, हम सिर्फ OpenAI API को बुलाते हैं, लेकिन आप इसे ML मॉडल से बदल सकते हैं locally।

&nbsp;

<div align="center">
<img src="https://raw.githubusercontent.com/reflex-dev/reflex/main/docs/images/dalle.gif" alt="A frontend wrapper for DALL·E, shown in the process of generating an image." width="550" />
</div>

&nbsp;

यहाँ पर इसका पूरा कोड है जिससे यह बनाया जा सकता है। यह सब एक ही Python फ़ाइल में किया गया है!

```python
import reflex as rx
import openai

openai.api_key = "YOUR_API_KEY"

class State(rx.State):
    """The app state."""
    prompt = ""
    image_url = ""
    processing = False
    complete = False

    def get_image(self):
        """Get the image from the prompt."""
        if self.prompt == "":
            return rx.window_alert("Prompt Empty")

        self.processing, self.complete = True, False
        yield
        response = openai.Image.create(prompt=self.prompt, n=1, size="1024x1024")
        self.image_url = response["data"][0]["url"]
        self.processing, self.complete = False, True
        

def index():
    return rx.center(
        rx.vstack(
            rx.heading("DALL·E"),
            rx.input(placeholder="Enter a prompt", on_blur=State.set_prompt),
            rx.button(
                "Generate Image",
                on_click=State.get_image,
                is_loading=State.processing,
                width="100%",
            ),
            rx.cond(
                State.complete,
                     rx.image(
                         src=State.image_url,
                         height="25em",
                         width="25em",
                    )
            ),
            padding="2em",
            shadow="lg",
            border_radius="lg",
        ),
        width="100%",
        height="100vh",
    )

# Add state and page to the app.
app = rx.App()
app.add_page(index, title="reflex:DALL·E")
app.compile()
```

## चलो इसे विस्तार से देखते हैं।

### **Reflex UI**

हम UI के साथ शुरू करेंगे।

```python
def index():
    return rx.center(
        ...
    )
```

यह `index` फ़ंक्शन एप्लिकेशन की फ़्रंटएंड को परिभाषित करता है।

हम फ़्रंटएंड बनाने के लिए `center`, `vstack`, `input`, और `button` जैसे विभिन्न components का उपयोग करते हैं। Components को एक-दूसरे के भीतर डाल सकते हैं विस्तारित लेआउट बनाने के लिए। और आप CSS की पूरी ताक़त के साथ इन्हें स्टाइल करने के लिए कीवर्ड आर्ग्यूमेंट (keyword args) का उपयोग कर सकते हैं।

रिफ़्लेक्स के पास [60+ built-in components](https://reflex.dev/docs/library) हैं जो आपको शुरुआती मदद के लिए हैं। हम बहुत से components जोड़ रहे हैं, और अपने खुद के components बनाना भी आसान है। [create your own components](https://reflex.dev/docs/wrapping-react/overview/)

### **स्टेट (State)**

Reflex आपके UI को आपकी स्टेट (state) के एक फ़ंक्शन के रूप में प्रस्तुत करता है।

```python
class State(rx.State):
    """The app state."""
    prompt = ""
    image_url = ""
    processing = False
    complete = False
```

स्टेट (state) ऐप में उन सभी वेरिएबल्स (vars) को परिभाषित करती है जो बदल सकती हैं और उन फ़ंक्शनों को जो उन्हें बदलते हैं।

यहां स्टेट (state) में `prompt` और `image_url` शामिल हैं। प्रगति और छवि दिखाने के लिए `processing` और `complete`  बूलियन भी हैं।

### **इवेंट हैंडलर (Event Handlers)**

```python
def get_image(self):
    """Get the image from the prompt."""
    if self.prompt == "":
        return rx.window_alert("Prompt Empty")

    self.processing, self.complete = True, False
    yield
    response = openai.Image.create(prompt=self.prompt, n=1, size="1024x1024")
    self.image_url = response["data"][0]["url"]
    self.processing, self.complete = False, True
```

स्टेट (state) के अंदर, हम इवेंट हैंडलर्स (event handlers) को परिभाषित करते हैं जो स्टेट वेरिएबल्स को बदलते हैं। इवेंट हैंडलर्स (event handlers) से reflex में स्टेट (state) को मॉडिफ़ाय किया जा सकता हैं। इन्हें उपयोगकर्ता क्रियाओं (user actions) के प्रति प्रतिक्रिया (response) के रूप में बुलाया जा सकता है, जैसे कि बटन को क्लिक करना या टेक्स्ट बॉक्स में टाइप करना। इन क्रियाओं को इवेंट्स (events) कहा जाता है।

हमारे DALL·E. ऐप में एक इवेंट हैंडलर `get_image` है जिससे यह OpenAI API से इमेज प्राप्त करता है। इवेंट हैंडलर में `yield` का उपयोग करने कि वजह से UI अपडेट हो जाएगा। अन्यथा UI इवेंट हैंडलर के अंत में अपडेट होगा।

### **रूटिंग (Routing)**

आखिरकार, हम अपने एप्लिकेशन को परिभाषित करते हैं।

```python
app = rx.App()
```

हम अपने एप्लिकेशन के रूट से इंडेक्स कॉम्पोनेंट तक एक पेज को जोड़ते हैं। हम एक शीर्षक भी जोड़ते हैं जो पेज प्रीव्यू/ब्राउज़र टैब में दिखाई देगा।

```python
app.add_page(index, title="DALL-E")
app.compile()
```

आप और पेज जोड़कर एक मल्टी-पेज एप्लिकेशन बना सकते हैं।

## 📑 संसाधन (Resources)

<div align="center">

📑 [Docs](https://reflex.dev/docs/getting-started/introduction) &nbsp; |  &nbsp; 🗞️ [Blog](https://reflex.dev/blog) &nbsp; |  &nbsp; 📱 [Component Library](https://reflex.dev/docs/library) &nbsp; |  &nbsp; 🖼️ [Gallery](https://reflex.dev/docs/gallery) &nbsp; |  &nbsp; 🛸 [Deployment](https://reflex.dev/docs/hosting/deploy)  &nbsp;   

</div>

## ✅ स्टेटस (Status)

रिफ्लेक्स को दिसंबर 2022 में पाइनकोन नाम से लॉन्च किया गया।

जुलाई 2023 तक, हम **Public Beta** (सार्वजनिक बीटा) चरण में हैं।

-   :white_check_mark: **Public Alpha** (सार्वजनिक अल्फा): कोई भी रिफ्लेक्स इंस्टॉल और उपयोग कर सकता है। कुछ इशू हो सकते हैं, लेकिन हम उन्हें सुलझाने के लिए सक्रिय रूप से काम कर रहे हैं।
-   :large_orange_diamond: **Public Beta** (सार्वजनिक बीटा): गैर-उद्यम उपयोग-मामलों के लिए स्थिर।
-   **Public Hosting Beta** (सार्वजनिक होस्टिंग बीटा): _Optionally_, अपने ऐप्स को रिफ्लेक्स पर डिप्लॉइ और होस्ट करें!
-   **Public** (सार्वजनिक): रिफ्लेक्स उत्पादन के लिए तैयार है।

रिफ्लेक्स में हर सप्ताह नई रिलीज़ और सुविधाएँ आ रही हैं! अपडेट रहने के लिए इस रिपॉजिटरी को :star: स्टार करें और समय-समय पर अवश्य देखें :eyes:।

## (योगदान) Contributing

हम हर तरह के योगदान का स्वागत करते हैं! रिफ्लेक्स कम्यूनिटी में शुरुआत करने के कुछ अच्छे तरीके नीचे दिए गए हैं।

-   **Join Our Discord** (डिस्कॉर्ड सर्वर से जुड़ें): Our [Discord](https://discord.gg/T5WSbC2YtQ) हमारा डिस्कॉर्ड रिफ्लेक्स प्रोजेक्ट पर सहायता प्राप्त करने और आप कैसे योगदान दे सकते हैं, इस पर चर्चा करने के लिए सबसे अच्छी जगह है।
-   **GitHub Discussions** (गिटहब चर्चाएँ): उन सुविधाओं के बारे में बात करने का एक शानदार तरीका जिन्हें आप जोड़ना चाहते हैं या ऐसी चीज़ें जो भ्रमित करने वाली हैं/स्पष्टीकरण की आवश्यकता है।
-   **GitHub Issues** (गिटहब समस्याएं): ये बग की रिपोर्ट करने का एक शानदार तरीका है। इसके अतिरिक्त, आप किसी मौजूदा समस्या को हल करने का प्रयास कर सकते हैं और एक पीआर सबमिट कर सकते हैं।

हम सक्रिय रूप से योगदानकर्ताओं की तलाश कर रहे हैं, चाहे आपका कौशल स्तर या अनुभव कुछ भी हो।

## लाइसेंस (License)

रिफ्लेक्स ओपन-सोर्स है और [अपाचे लाइसेंस 2.0] (लाइसेंस) के तहत लाइसेंस प्राप्त है।
