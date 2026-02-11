# 🕵️ Polymarket Mispricing Hunter

Dashboard de **Caça a Desprecificações Temporárias** para mercados de predição Polymarket.

**Live Demo:** https://emalaman.github.io/mispricing-hunter/

---

## 🎯 O Que É?

**Mispricing Hunting** (Caça a Desprecificações) é uma estratégia de **arbitragem estatística** que explora momentos em que a soma dos preços dos contratos **YES** e **NO** não iguala exatamente **1.0** (100%).

### Por Que Isso Acontece?

- 🕒 **Latência da API**: Atualizações de preço não perfeitamente sincronizadas
- 📉 **Baixa liquidez**: Poucos traders → preços não se equilibrarão
- 🆕 **Mercados recém-criados**: Ineficiência inicial
- 🤖 **Bots desbalanceados**: Algoritmos que atualizam apenas um lado

**Exemplo real:**
```
YES: 0.45
NO:  0.50
Total: 0.95  ← Desprecificação de 5%!
```

Aqui,ammings existiria comprando **ambas** as posições a 0.45+0.50=0.95 e vendendo quando voltar a 1.00 (lucro de ~5% menos fees).

---

## ✨ Funcionalidades

- 🔍 **Detecção automática** de mercados com soma != 1.0
- 📊 **Gap (%)**: Mostra o desvio da normalidade
- 💡 **Sinal claro**: 
  - `🟣 COMPRAR AMBAS` se total < 0.98 (desconto)
  - `🔴 VENDER AMBAS` se total > 1.02 (sobrepreço)
- ⏱️ **Refresh a cada 15 segundos** (rápido!)
- 📱 **Responsivo** - mobile-friendly
- 🎛️ **Filtros configuráveis**:
  - Gap mínimo (%)
  - Volume mínimo ($)
  - Tempo restante (24h, 7d, 30d)
- 📈 **Stats em tempo real**: total monitorados, oportunidades, média de gap, melhor op
- 🚀 **100% estático** - roda no GitHub Pages

---

## 🛠️ Tecnologia

- HTML5 + Vanilla JavaScript
- Tailwind CSS (CDN)
- Gamma API pública (sem autenticação)

---

## 🚀 Deploy (GitHub Pages)

1. **Use este repo como template** ou clone:
   ```bash
   git clone https://github.com/emalaman/mispricing-hunter.git
   ```
2. **Push para seu próprio repositório** no GitHub
3. **Ative GitHub Pages**:
   - Settings → Pages
   - Source: Deploy from a branch → `main` → `/ (root)`
4. Acesse: `https://SEUUSER.github.io/mispricing-hunter/`

---

## 🔍 Como Funciona

### Lógica de Detecção

1. Busca todos os mercados ativos da Gamma API
2. Para cada mercado:
   - Extrai preços YES e NO
   - Calcula `total = YES + NO`
   - Calcula `gap = |total - 1.0| * 100` (percentual de desvio)
3. Se `gap >= gap_min` (padrão: 2%) → **oportunidade**
4. Determina sinal:
   - `total < 0.98` → **COMPRAR AMBAS** (YES + NO)
   - `total > 1.02` → **VENDER AMBAS** (YES + NO)
   - Caso contrário → sem sinal

### Estratégia de Trading

**Cenário 1: Total < 1.0 (ex: 0.95)**
- Comprar **YES** e **NO** simultaneamente
- Cortar automaticamente quando o total retornar a ~1.00
- Lucro esperado: `(1.00 - total) / total` ≈ 5.26% no exemplo

**Cenário 2: Total > 1.0 (ex: 1.05)**
- Vender **YES** e **NO** (short both)
- Comprar de volta quando total = 1.00
- Lucro esperado: `(total - 1.00) / total` ≈ 4.76% no exemplo

⚠️ **Aviso:** Esta lógica teórica. Na prática, fees, spreads e latency comem parte do lucro.

---

## 🎛️ Filtros

| Filtro | Descrição | Padrão |
|--------|-----------|--------|
| **Gap Mínimo** | % mínimo de desvio para aparecer | 2% |
| **Volume Mínimo** | Volume USD mínimo | $10,000 |
| **Tempo Restante** | Filtro de duração | Todos |

Ajuste os filtros no topo do dashboard para focar em:
- **Gap alto**: Oportunidades maiores (mais raras)
- **Volume alto**: Mais liquidez (fácil entrada/saída)
- **Tempo curto**: Mercados que expiram em breve (mais urgente)

---

## ⚙️ Customização

### Alterar intervalo de atualização

Em `index.html`, linha do `setInterval`:

```javascript
setInterval(loadData, 15000); // 15000ms = 15 segundos
```

### Ajustar thresholds de sinal

Na função `getArbSignal()`:

```javascript
if (prices.total < 0.98) { /* comprar ambas */ }
// Mudar para 0.99 ou 0.97 conforme sua tolerância
```

### Adicionar mais criptomoedas

Atualmente mostra todos os mercados. Para filtrar por cripto, adicione em `render()`:

```javascript
const isCrypto = /bitcoin|btc|ethereum|eth|solana|sol|dogecoin|doge/.test(m.question.toLowerCase());
if (!isCrypto) continue;
```

---

## 📊 Métricas Exibidas

| Métrica | O que Significa |
|---------|-----------------|
| **YES/NO** | Preços individuais dos contratos |
| **Total** | Soma dos dois preços (deveria ser 1.0) |
| **Gap** | `|Total - 1.0| × 100` - tamanho da oportunidade |
| **Volume** | Liquidez do mercado (USD) |
| **Tempo restante** | Quanto falta para o mercado expirar |
| **Sinal** | Ação recomendada baseada no gap |

---

## 🎓 Estratégia de Execução

### Entrada

1. **Identifique** um mercado com gap significativo (ex: total = 0.95)
2. **Verifique** volume e tempo restante
3. **Execute**:
   - Comprar YES a preço de mercado
   - Comprar NO a preço de mercado
   - Custo total: 0.95 por unidade de cada contrato

### Saída

Quando o total voltar a ~1.00:

1. Vender **YES** a ~0.50
2. Vender **NO** a ~0.50
3. Receita total: 1.00
4. **Lucro bruto**: 1.00 - 0.95 = 0.05 por contrato (5.26%)

### Considerações Práticas

- **Fees do Polymarket**: ~2% por trade (entrada + saída = ~4% total)
- **Spread**: Diferença entre melhor bid/ask
- **Slippage**: Movimento de preço durante sua execução

**Exemplo real:**
```
Gap: 5% (total 0.95)
Fees: 4%
Slippage: 0.5%
Net: ~0.5% de lucro (se tudo executes rápido)
```

Portanto, **gap > 4%** é mais interessante. Gaps pequenos (2%) podem não valer a pena após fees.

---

## ⚠️ Riscos e Limitações

| Risco | Descrição | Mitigação |
|------|-----------|-----------|
| **Gap desaparece rápido** | Outros bots podem arbitrar antes | Execução manual rápida ou bot automatizado |
| **Fees altos** | 2% por trade reduz margem | Só operar gaps > 4% |
| **Liquidez baixa** | Dificuldade para entrar/sair | Filtro volume mínimo |
| **Market expira** | Se não normalizar antes do fim | Evitar mercados com < 1h restante |
| **Gamma API delay** | Dados podem ter atraso | Refresh rápido (15s) ajuda |

---

## 🐛 Troubleshooting

**Q: Não aparecem oportunidades**  
R: Ajuste o "Gap Mínimo" para 1% ou 0.5%. Ou aumente o volume mínimo se a rede estiver saturada.

**Q: Sinal mudou de comprar para vender**  
R: O gap pode ter se invertido (total passou de 0.95 para 1.05). Normal emmercados rápidos.

**Q: Posso automatizar trades?**  
R: Não implementado. Você precisaria integrar com a API de trading do Polymarket (CLOB) e automatizar a execução. Cuidado com rate limits e riscos.

**Q: Por que o dashboard some/"Nenhum mercado"?**  
R: A Gamma API pode ter falhado ou não há mercados ativos no momento. Tente novamente em 15s.

---

## 📚 Referências

- **Gamma API:** https://gamma-api.polymarket.com/
- **Polymarket Docs:** https://docs.polymarket.com/
- **Statistical Arbitrage:** https://www.investopedia.com/terms/s/statisticalarbitrage.asp
- **Mean Reversion:** https://www.investopedia.com/terms/m/meanreversion.asp

---

## 📈 Exemplo de Oportunidade Real (Hipotético)

```
Mercado: "Will Fed raise rates in March?"
YES: 0.48
NO:  0.47
Total: 0.95  ← Gap de 5%

Sinal: 🟣 COMPRAR AMBAS
Ação: Comprar YES @0.48 + NO @0.47 = custo $0.95
Alvo: Vender ambos quando total = 1.00 (receita $1.00)
Lucro bruto: 5.26%
Após fees (4%): ~1.26% líquido
Tempo típico de normalização: 1-10 minutos
```

---

## 🤝 Contribuindo

Ideias para melhorar:
- [ ] Adicionarapá de histórico de gaps por mercado
- [ ] Alertas sonoros/quando gap > X%
- [ ] Integração com API de trading (automation)
- [ ] Filtro por categoria (cripto, política, esportes)
- [ ] Gráfico de evolução do gap ao longo do tempo

---

## 📄 Licença

MIT

---

**Criado por EmilIA** • 2026-02-11

Dashboard: https://emalaman.github.io/mispricing-hunter/

**⚠️ Disclaimer:** Esta ferramenta é para análise apenas. Não é recomendação de investimento. Você é responsável por suas próprias decisões de trading. Nunca arrisque mais do que pode perder.
