# 🎸 RigTone NAM — v5

Rig de guitarra completo no navegador: pedalboard modular, cabeçote valvulado virtual,
**Neural Amp Modeler** (perfis neurais de amps reais via WebAssembly), cab sim por
convolução (IR), looper estéreo e gravação .wav — tudo com motor AudioWorklet.

## Como rodar

Requisito: **Node.js 18+** (sem `npm install`, zero dependências).

```bash
node server.js
```

Abre **http://localhost:8123** no Chrome, pluga a guitarra, usa **fone de ouvido**
e clica em "Ligar o rig".

> ⚠️ **Não funciona abrindo o index.html direto** (file://). O NAM usa
> SharedArrayBuffer, que exige os headers COOP/COEP que o `server.js` envia.

## O módulo NAM Neural

O painel **NAM Neural** fica entre o amp valvulado e o Cab IR. Ele roda o motor do
[Neural Amp Modeler](https://www.neuralampmodeler.com/) compilado pra WASM
(fork da TONE3000), com inferência em tempo real dentro de um AudioWorklet.

**Perfis inclusos** (em `public/models/`):

| Arquivo      | Amp                  |
|--------------|----------------------|
| jcm.nam      | Marshall JCM800      |
| deluxe.nam   | Fender Deluxe Reverb |
| 5153.nam     | EVH 5150 III         |
| dumble.nam   | Dumble ODS           |
| ac10.nam     | Vox AC10             |
| jc.nam       | Roland JC-120        |
| ampeg.nam    | Ampeg (baixo)        |

**Mais perfis (milhares, grátis):** https://www.tone3000.com (ToneHunt).
Baixa o `.nam` e arrasta em cima do painel NAM, ou usa o botão **ABRIR .NAM**.
Pra deixar um perfil fixo no dropdown: joga o arquivo em `public/models/` e
adiciona uma linha no `public/models/index.json`.

**Dica de uso:** com o NAM ativo, desliga o **POWER** do amp valvulado — assim você
ouve só o perfil neural. O Cab IR continua valendo depois do NAM (perfis "amp only"
precisam de IR; perfis "full rig" já vêm com caixa, aí desliga o Cab).

## Estrutura

```
rigtone-nam/
├── server.js              # servidor local com headers COOP/COEP
├── package.json
└── public/
    ├── index.html         # o rig inteiro (UI + DSP + worklets)
    ├── t3k-wasm-module.*  # motor NAM compilado pra WASM (TONE3000)
    └── models/            # perfis .nam + index.json
```

## Deploy na web (opcional)

Funciona em qualquer host que permita configurar headers. Na Vercel, cria um
`vercel.json` com:

```json
{
  "headers": [{
    "source": "/(.*)",
    "headers": [
      { "key": "Cross-Origin-Opener-Policy", "value": "same-origin" },
      { "key": "Cross-Origin-Embedder-Policy", "value": "require-corp" }
    ]
  }]
}
```

e publica a pasta `public/` como estático.

## Créditos

- Motor NAM: [Neural Amp Modeler Core](https://github.com/sdatkinson/NeuralAmpModelerCore)
  (Steven Atkinson, MIT) via fork WASM da
  [TONE3000](https://github.com/tone-3000/neural-amp-modeler-wasm) (MIT).
- Perfis .nam de exemplo: repositório TONE3000.
- Rig, DSP dos pedais, amp virtual, looper e UI: construído com Claude.
