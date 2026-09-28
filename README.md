# Supplier Scoring & Ranking Tool (V1)

Ferramenta em Python que lê uma matriz de scoring em Excel e gera um **ranking de fornecedores** com score ponderado, status de compliance e pontos de atenção para negociação.

Nasceu de uma dor real de compras: comparar propostas de vários fornecedores de forma padronizada, rápida e com critério explícito, sem depender de planilha manual e de "feeling".

> **Status:** V1. Automatiza o scoring, o compliance e o relatório. Ainda **não usa modelo de linguagem** (ver "Próximos passos").

## O que ele faz

- Lê os dados dos fornecedores direto do Excel
- Calcula a **Nota de Preço** automaticamente (menor preço ÷ preço do fornecedor × 100)
- Calcula o **score final ponderado**
- Ordena o ranking colocando fornecedores **CONFORME** antes dos **COM RESSALVA**, para que um fornecedor fora dos requisitos não apareça como melhor opção só por ter nota alta
- Mostra leitura, oportunidade de negociação e ressalva de cada fornecedor
- Alerta inconsistências na planilha (ex.: escopo com ressalva, mas nota de escopo 100)

## Critérios de pontuação

| Critério   | Peso | Regra                                             |
|------------|------|---------------------------------------------------|
| Preço      | 30%  | menor preço ÷ preço do fornecedor × 100           |
| SLA        | 25%  | ≤3h = 100 · 4h = 90 · 5h = 70 · 6h = 50 · >6h = 0 |
| Pagamento  | 15%  | ≥60d = 100 · 45d = 90 · 30d = 80 · 15d = 40 · <15d = 0 |
| Escopo     | 15%  | 100 = atende integralmente                        |
| Garantia   | 10%  | 12m = 80 · 18m = 90 · 24m = 100                   |
| Cobertura  | 5%   | Grande SP = 100                                   |

## Estrutura da planilha

| Aba             | Conteúdo                                              |
|-----------------|-------------------------------------------------------|
| `Scoring`       | Preço e notas de cada fornecedor                      |
| `Dados_e_Notas` | Dados originais das propostas (SLA, prazo, garantia)  |
| `Compliance`    | Checklist de requisitos e status                      |
| `Resultado`     | Leitura, oportunidade e ressalva por fornecedor       |

Os dados do repositório são **fictícios**.

## Como rodar

1. Instale a dependência:
   ```
   pip install openpyxl
   ```
2. Deixe `procurement_agent.py` e `matriz_scoring_procurement.xlsx` na mesma pasta
3. Execute:
   ```
   python procurement_agent.py
   ```

## Exemplo de saída

```
ANÁLISE AUTOMÁTICA — AI PROCUREMENT AGENT
O score apoia a decisão e não substitui validação técnica/comercial.

1. Fornecedor C Engenharia Ltda. | Score 92.78 | R$ 235.000 | CONFORME
   Leitura: Boa condição financeira e garantia superior.
   Oportunidade: Negociar preço e validar benefícios.
   Ressalva: Garantia de 24 meses precisa de validação técnica.
2. Fornecedor A Serviços Ltda. | Score 89.5 | R$ 220.000 | CONFORME
   ...
3. Fornecedor D Serviços Técnicos Ltda. | Score 84.05 | R$ 247.000 | COM RESSALVA
   ...
4. Fornecedor B Facilities S.A. | Score 84.0 | R$ 198.000 | COM RESSALVA
   ...

ATENÇÃO — possível inconsistência na planilha:
   Fornecedor B Facilities S.A.: escopo = 'Ressalva' mas Nota Escopo = 100
```

## Como adicionar fornecedores

Basta editar o Excel, sem mexer no código. O fornecedor precisa aparecer nas **4 abas com o nome idêntico**. A Nota de Preço de todos é recalculada sozinha.

## Limitações conhecidas

- As notas de SLA, pagamento, garantia e cobertura ainda são digitadas manualmente, seguindo a tabela de regras
- Os textos de leitura/oportunidade/ressalva vêm da planilha, não são gerados
- O score apoia a decisão, mas **não substitui** validação técnica e comercial

## Próximos passos

- Calcular todas as notas automaticamente a partir dos dados das propostas
- Integrar um modelo de linguagem (API do Claude por exemplo) para ler propostas em PDF/e-mail, extrair preço, SLA e prazo, e gerar a leitura e a estratégia de negociação
- Exportar o relatório para Excel/PDF
