# TGSI-TBR
# Testes Baseados em Risco no Módulo de Inventário do Odoo Community 19.0

Repositório de apoio ao Trabalho de Graduação em Sistemas de Informação (TGSI):

> **Aplicação de Testes Baseados em Risco (TBR) Apoiados por Inteligência Artificial Generativa para priorização de Casos de Teste no Módulo de Gestão de Inventário do ERP Odoo Community**

- **Autora:** Emily Cristina Ribeiro
- **Orientador:** Dr. Cristiano Bertolini
- **Instituição:** Universidade Federal de Santa Maria (UFSM), Campus Frederico Westphalen, Bacharelado em Sistemas de Informação


---

## Sobre o projeto

O estudo de caso aplica **Testes Baseados em Risco (TBR)** ao módulo de Inventário (`stock`) do Odoo Community. As funcionalidades são agrupadas em componentes de risco e classificadas por **probabilidade de falha (P)** e **impacto no negócio (I)**, em escala de 1 a 5, com **Fator de Risco = P × I**. Um agente de IA generativa (Gemini) apoia a classificação, e todas as sugestões são comparadas com a avaliação manual e validadas pela testadora. A matriz orienta o Plano de Testes, estruturado segundo a série **ISO/IEC/IEEE 29119**.

Este repositório reúne os artefatos que sustentam o artigo, para consulta e reprodutibilidade.

## Conteúdo do repositório

> Ajuste os nomes abaixo para refletir a estrutura real do repositório.

```
.
├── README.md
├── plano-de-testes/
│   └── Plano_de_Testes.pdf          # Plano de Testes (nível de projeto)
├── matriz-de-riscos/
│   ├── matriz_gemini                # Matriz gerada com apoio do Gemini
│   └── matriz_consolidada           # Matriz final (avaliação da testadora + decisões)
├── prompt-gemini/
│   └── prompt.md                    # Prompt usado na classificação de riscos
├── caderno-de-testes/
│   └── Caderno_de_Testes.xlsx       # Casos TC-01 a TC-21, resultados e status
├── evidencias/
│   └── TC-XX_passoN_descricao.png   # Capturas de tela dos testes executados
├── defeitos/
│   └── registros_de_defeito         # Defeitos registrados, com severidade
└── relatorios/
    └── Relatorio_de_Conclusao.pdf   # Relatório de conclusão de teste
```

## Componentes de risco e escopo

| ID | Componente (modelos Odoo) | P | I | P×I | Nível | Casos |
|----|---------------------------|---|---|-----|-------|-------|
| RC01 | Motor de Movimentações (`stock.picking`, `move`) | 4 | 5 | 20 | Crítico | TC-01 a TC-05 |
| RC02 | Regras Logísticas Avançadas (`stock.route`, `rule`) | 5 | 4 | 20 | Crítico | TC-10 a TC-13 |
| RC03 | Gestão de Saldos e Ajustes (`stock.quant`) | 3 | 5 | 15 | Alto | TC-06 a TC-09 |
| RC04 | Rastreabilidade e Pacotes (`stock.lot`, `package`) | 3 | 4 | 12 | Alto | TC-20, TC-21 (e TC-09 para lote) |
| RC05 | Estrutura Física e Locais (`stock.warehouse`) | 2 | 5 | 10 | Médio | TC-17 a TC-19 |
| RC06 | Reabastecimento e Pendências (`orderpoint`) | 2 | 4 | 8 | Médio | TC-14 a TC-16 |
| RC07 | Operações Excepcionais (`stock.scrap`, `return`) | 2 | 3 | 6 | Baixo | Não testado (risco residual) |

**Faixas de nível:** Baixo 1–6 · Médio 7–10 · Alto 11–15 · Crítico 16–25.

**Escopo:** 21 casos planejados (9 Críticos, 6 Altos e 6 Médios). Os casos de risco Médio são uma amostra de controle para comparação entre níveis. O nível Baixo não foi testado neste ciclo. Ficam fora do escopo testes de desempenho, carga e segurança (exceto o isolamento entre empresas em TC-19), recursos exclusivos da edição Enterprise, integrações externas e automação da execução.

## Ambiente de teste

| Item | Configuração |
|------|--------------|
| Sistema | Odoo Community **19.0** (instalador `19.0.20260529`) |
| Instalação | Local, Windows, PostgreSQL local, porta 8069 |
| Dados | Sem dados de demonstração |
| Código-fonte | Sem modificação |
| Aplicativos de apoio | Vendas, Compras, Faturamento e Dropshipping, instalados sob demanda |
| Execução | Manual, pela interface web (nível de sistema) |

## Como reproduzir

1. Instale o Odoo Community 19.0 com PostgreSQL, **sem dados de demonstração**, e crie o banco de dados.
2. Instale o aplicativo **Inventário**. Instale Vendas, Compras, Faturamento e Dropshipping apenas quando um caso exigir.
3. Crie os dados de teste descritos na Seção 6.6 do plano (produtos, contatos, segundo usuário, segunda empresa, hierarquia de localizações, rota de recebimento em 2 etapas, regra de reabastecimento e categoria com valoração automatizada).
4. Execute os casos do Caderno de Testes **na ordem de risco**: Crítico, depois Alto, depois Médio.
5. Registre para cada caso: resultado, status (**Passou**, **Falhou** ou **Bloqueado**), evidência e, quando houver falha, o defeito com severidade.

### Convenção de evidências

Capturas de tela nomeadas como `IDdoCaso_passo_descricao.png`, por exemplo `TC-01_passo3_destino.png`, na pasta `evidencias/`.

### Escala de severidade dos defeitos

| Severidade | Critério |
|------------|----------|
| Bloqueante | Impede continuar o fluxo ou compromete a integridade do saldo de estoque |
| Alta | Resultado incorreto sem solução simples |
| Média | Comportamento incorreto com contorno disponível |
| Baixa | Mensagem confusa ou problema cosmético |

Os defeitos são **apenas registrados**, e não corrigidos.

## Métricas

| ID | Métrica | Definição |
|----|---------|-----------|
| M1 | Cobertura de execução por nível | Casos executados ÷ casos planejados |
| M2 | Densidade de defeitos por nível | Defeitos encontrados ÷ casos executados (compara Alto+Crítico com Médio) |
| M3 | Taxa de falha por nível | Casos com falha ÷ casos executados |
| M4 | Taxa de bloqueio | Casos bloqueados ÷ casos planejados |
| M5 | Esforço por caso | Tempo de execução em minutos |
| M6 | Defeitos por severidade | Contagem por categoria |

## Uso do agente de IA

| Item | Registro |
|------|----------|
| Modelo | Gemini Plus |
| Data da consulta | 15/09/2026 |
| Entrada | Descrição funcional extraída do Odoo via XML-RPC |
| Prompt | `prompt-gemini/prompt.md` |
| Avaliação independente da testadora | 30/09/2026 |

A IA atua como **apoio**. O valor final de cada nota é validado pela testadora e revisado pelo orientador, e cada divergência recebe justificativa escrita.

## Base normativa

- ISO/IEC/IEEE 29119-1:2022 — Conceitos gerais
- ISO/IEC/IEEE 29119-2:2021 — Processos de teste
- ISO/IEC/IEEE 29119-3:2021 — Documentação de teste
- ISO/IEC/IEEE 29119-4:2021 — Técnicas de teste

O plano segue a estrutura de *Test Plan* da 29119-3 e os processos da 29119-2, com adaptação para um estudo acadêmico conduzido por uma única testadora. Itens que pressupõem estrutura organizacional (política de teste e estratégia de teste organizacional) não foram elaborados.

