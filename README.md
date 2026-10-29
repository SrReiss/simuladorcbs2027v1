# Simulador CBS 2027

Simulador web que compara, numa venda, o **PIS/COFINS de hoje** com a **CBS de 2027** (Reforma Tributária, LC 214/2025). Ele mostra o preço ao cliente, a margem e a carga efetiva, já com os créditos sobre compras, frete e serviços tomados.

É uma página única em HTML, CSS e JavaScript puro: não precisa de servidor, instalação nem dependências.

![Tela do Simulador CBS 2027](docs/screenshot.png)

---

## Funcionalidades

- **Hoje x 2027 lado a lado:** preço dos produtos, preço ao cliente, tributos, líquido, custo, créditos e margem.
- **Carga efetiva:** débito − créditos (compras, frete, serviços tomados) − devoluções, para PIS/COFINS e CBS, mais ICMS + DIFAL (ou ISS) e o total sobre o consumo.
- **Comércio ou serviço:** ICMS e DIFAL na venda a não contribuinte, ou ISS.
- **Regime de PIS/COFINS atual:** não cumulativo (9,25%, com créditos) ou cumulativo (3,65%, sem créditos).
- **Cenários de preço** da própria empresa e do fornecedor, com um **comparativo das quatro combinações**. Clicando numa linha do comparativo, ela passa a ser o cenário analisado.
- **Alíquota de referência configurável:** o simulador aplica automaticamente a redução de 0,1 ponto percentual de 2027.
- **Premissas avançadas:** CBS na base do ICMS/ISS, exclusão do ISS da base do PIS/COFINS (Tema 118) e PIS/COFINS embutido no preço do fornecedor.
- Aceita números no formato brasileiro (`2.500,00`) e guarda os últimos valores digitados no próprio navegador.

---

## Como usar

### Localmente
Baixe o repositório e abra o `index.html` em qualquer navegador.

### GitHub Pages
1. No repositório, vá em **Settings → Pages**.
2. Em **Build and deployment**, escolha **Deploy from a branch**.
3. Selecione a branch `main` e a pasta `/ (root)` e clique em **Save**.
4. Em alguns minutos o simulador fica disponível em `https://<usuario>.github.io/<repositorio>/`.

---

## Entradas

| Grupo | Campo | Observação |
|---|---|---|
| Venda | Valor de venda | Preço atual cobrado do cliente |
| Venda | Atividade | Comércio (ICMS e DIFAL) ou Serviço (ISS) |
| Venda | ICMS da venda / DIFAL + FCP | Comércio. DIFAL na venda a não contribuinte de outro estado, com o FCP do destino |
| Venda | ISS da venda | Serviço |
| PIS/COFINS hoje | Regime | Não cumulativo 9,25% ou cumulativo 3,65% |
| Compras e despesas | Custo da compra e ICMS na compra | % sobre a venda |
| Compras e despesas | Frete e ICMS do frete | % sobre a venda |
| Compras e despesas | Serviços tomados e ISS dos serviços | % sobre a venda |
| Compras e despesas | Devoluções de venda | % sobre a venda |
| CBS 2027 | Alíquota de referência | A aplicada é a referência − 0,1 p.p. |
| CBS 2027 | Preço de venda próprio | Ver [Cenários](#cenários) |
| CBS 2027 | Preço do fornecedor | Ver [Cenários](#cenários). Vale para compras, frete e serviços |

Informe em compras, frete e serviços apenas o que gera crédito de CBS, ou seja, fornecedores e prestadores do **regime regular**. Optantes do Simples Nacional sem a opção pelo regime regular geram crédito limitado.

Os valores que aparecem ao abrir a página são apenas exemplos. O botão **Restaurar valores padrão** volta para eles.

---

## Cenários

**Preço de venda próprio**

| Cenário | Preço dos produtos (F) | Preço ao cliente |
|---|---|---|
| Mantém o preço e soma a CBS por fora | F = preço atual | F + CBS |
| Tira o PIS/COFINS e soma a CBS por fora | F = preço − alíquota do regime × (preço − ICMS − DIFAL) | F + CBS |

**Preço do fornecedor** (compras, frete e serviços tomados)

| Cenário | Novo valor da compra |
|---|---|
| Mantém o preço e soma a CBS por fora | valor atual + CBS |
| Tira o PIS/COFINS e soma a CBS por fora | (valor − % embutido × (valor − ICMS)) + CBS. Nos serviços: valor − % embutido × valor |

---

## Metodologia

### Alíquota da CBS
Em 2027 e 2028 a alíquota aplicada é a de referência menos **0,1 ponto percentual**.

### CBS por fora com ICMS/ISS na base
Com a premissa de que a CBS integra a base do ICMS (ou do ISS), sendo **F** o preço dos produtos, **a** a alíquota de ICMS + DIFAL (ou ISS) e **t** a alíquota da CBS:

```
base CBS         = F × (1 − a) ÷ (1 + a × t)
CBS              = base × t
preço ao cliente = F + CBS
ICMS/ISS         = a × preço ao cliente
```

Sem essa premissa: `base = F × (1 − a)`, e o ICMS/ISS incide apenas sobre F.

O mesmo cálculo é usado para a CBS cobrada pelos fornecedores, que é o crédito da empresa.

### PIS/COFINS hoje
- **Débito:** alíquota do regime × (preço − ICMS − DIFAL). Na empresa de serviço, com o Tema 118 ativado, o ISS também sai da base.
- **Não cumulativo:** crédito de 9,25% sobre compras e frete, sem o ICMS. Os serviços tomados geram crédito apenas quando a empresa é de serviço.
- **Cumulativo:** sem créditos. As devoluções continuam reduzindo a base.

### Devoluções
Espelham a venda: estornam o mesmo percentual de PIS/COFINS, CBS e ICMS próprio da operação original. O DIFAL pago ao estado de destino não é recuperado.

### Margem e carga efetiva
- **Margem** = líquido da venda − custo da compra + créditos sobre a compra (PIS/COFINS ou CBS, e ICMS no comércio). Frete, serviços e devoluções entram só na carga efetiva.
- **Carga efetiva** = débito − créditos − devoluções.
- Percentuais de **hoje** são calculados sobre o preço de venda; os de **2027**, sobre o preço ao cliente (vendas brutas do cenário).

---

## Premissas e limitações

- **Alíquota de referência:** o padrão é o percentual calculado pelo TCU em 28/10/2026 (11,3468%). A fixação final cabe ao Senado Federal.
- **CBS na base do ICMS/ISS:** reflete entendimentos já manifestados por algumas Fazendas estaduais. O tema ainda está em discussão (PLP 16/2025). Pode ser desligada nas premissas avançadas.
- **ISS na base do PIS/COFINS:** o STF excluiu o ISS da base no Tema 118 (21/10/2026), mas a modulação dos efeitos ainda não foi decidida.
- **ICMS:** o simulador usa as alíquotas informadas. Benefícios como crédito presumido não são considerados, então o ICMS destacado pode ser maior que o custo efetivo.
- **Simulação unitária:** os percentuais de compras, frete, serviços e devoluções representam médias, e o crédito real depende do período de apuração e do volume de compras do mês.
- **IBS:** fora do escopo desta versão, que trata apenas da CBS.

> **Aviso:** este simulador apoia decisões de preço e análises de impacto. Ele não substitui a apuração dos tributos nem a análise da legislação aplicável a cada operação.

---

## Base legal e referências

- Emenda Constitucional nº 132/2023 (Reforma Tributária)
- Lei Complementar nº 214/2025 (IBS, CBS e Imposto Seletivo)
- STF, Tema 69 (exclusão do ICMS da base do PIS/COFINS)
- STF, Tema 118 (exclusão do ISS da base do PIS/COFINS)

---

## Tecnologia

- HTML, CSS e JavaScript puro, num único arquivo (`index.html`)
- Fontes IBM Plex Sans e IBM Plex Mono via Google Fonts (sem internet, o navegador usa uma fonte do sistema)
- Os valores digitados ficam salvos apenas no navegador de quem usa (`localStorage`); nenhum dado é enviado a servidores

## Estrutura

```
.
├── index.html          # o simulador
├── README.md
└── docs/
    └── screenshot.png  # imagem usada neste README
```

---

## Autor

Maicon Felipe Teixeira dos Reis
