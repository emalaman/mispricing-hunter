# 🚀 Ativar GitHub Pages - Mispricing Hunter

Siga estes passos para ativar o site:

---

## 1. Acesse as configurações do repositório

Abra no navegador:
https://github.com/emalaman/mispricing-hunter/settings/pages

---

## 2. Configure o Pages

Em **Build and deployment**:

- **Source**: selecione `Deploy from a branch`
- **Branch**: `master` (ou `main`) 
- **Folder**: `/ (root)`

Clique em **Save**

---

## 3. Aguarde

- Status mudará para: **"Your site is ready to be published at..."**
- Leva de 1 a 3 minutos
- O site estará em:
  ```
  https://emalaman.github.io/mispricing-hunter/
  ```

---

## 4. Teste

Acesse o link acima. Deverá ver o dashboard "🕵️ Polymarket Mispricing Hunter".

---

## 📁 Estrutura do repositório

```
mispricing-hunter/
├── index.html    ← Dashboard principal
├── README.md     ← Documentação
└── .gitignore    ← (opcional)
```

O GitHub Pages serve o `index.html` diretamente da raiz do branch.

---

## ✅ Pronto!

Nenhuma secret ou configuração adicional é necessária.

O dashboard auto-atualiza a cada 15 segundos (via JavaScript no navegador).

---

**Problema?** Verifique se o branch `master` existe e tem o arquivo `index.html` na raiz.
