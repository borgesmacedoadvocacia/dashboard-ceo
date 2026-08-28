# Dashboard — Ambiente do CEO

Painel de comando do escritório Borges Macedo Advocacia. Consolida **seis planilhas** em
um só quadro e transforma os números em decisões: cada alerta traz diagnóstico, causa
provável, proposta de solução em passos, responsável sugerido, prazo e o indicador que
comprova que a medida funcionou.

**Acesso exclusivo das lideranças.** Só os perfis **Administração** e **Lideranças** têm
cofre neste painel.

## Fontes consolidadas

| Planilha | O que entra |
|---|---|
| Alvarás e Acordos | Receita creditada, honorários, take rate, repasses pendentes |
| CRM | Funil da análise técnica ao contrato: viabilidade, agendamento, win rate, no-show |
| Controladoria Jurídica | Prazo de protocolo das iniciais, onboarding, audiências |
| Diligências | Backlog, cumprimento, cobertura de contato, concentração por pessoa |
| Execuções de Sentença | Capital em execução, prazos vencidos, contrapartes |
| Base de Clientes | Tamanho da carteira e contatos sem identificação |

## Seções

| Seção | Para que serve |
|---|---|
| Termômetro do Escritório | Nota de 0 a 100 por área (Comercial, Operacional, Financeiro, Risco), calculada pelas penalidades dos alertas ativos |
| Métricas de Comando | Os oito números que definem o mês: honorários 12 meses, valor em execução, take rate, capital parado, win rate, protocolo no prazo, ticket médio e backlog |
| Onde o Dinheiro Está Parado | Filas de capital reconhecido que ainda não é caixa, separando **o que depende só do escritório** do que depende do Judiciário |
| Central de Alertas | Alertas ordenados por severidade e impacto, filtráveis e pesquisáveis. **Clique em qualquer um** para o diagnóstico completo e a proposta de solução |
| Agenda do CEO | Só o que tem prazo "Hoje" ou "Esta semana" — as decisões que não podem esperar |
| Vazamentos do Funil | Da análise produzida à inicial protocolada no prazo, com a perda absoluta de cada degrau |
| Ritmo do Escritório | 12 meses de honorários, contratos e produção técnica, para separar tendência de oscilação |
| Concentração e Dependências | Riscos que não aparecem no mês: contraparte dominante e carga concentrada em uma pessoa |
| Confiabilidade dos Dados | O que hoje impede uma leitura 100% confiável, fonte por fonte |

## Anatomia de um alerta

Cada alerta responde a cinco perguntas, nesta ordem:

1. **O que os dados mostram** — o indicador atual e o valor envolvido.
2. **Por que isso importa** — a consequência real para o escritório ou para o cliente.
3. **Causa provável** — a hipótese de raiz, não o sintoma.
4. **Proposta de solução** — passos concretos, não recomendações genéricas.
5. **Como acompanhar** — o indicador de sucesso, o responsável sugerido e o prazo.

### Severidade

- **Crítico** (−30 na nota da área): risco de dano irreversível ao cliente, ao caixa ou à reputação.
- **Alto** (−15): perda financeira relevante ou compromisso descumprido de forma sistemática.
- **Médio** (−6): fragilidade estrutural que ainda não produziu dano.

## Como os valores devem ser lidos

- **"em jogo"** (vermelho) — dinheiro que **existe** e está parado numa fila.
- **"potenciais"** (verde) — receita **estimada** a partir do ticket médio e da taxa de
  conversão atuais. É projeção, não saldo.
- Os valores dos alertas **não são somáveis entre si**: carteiras diferentes podem conter
  o mesmo processo. Por isso o rodapé mostra o maior valor isolado, nunca um total.

## Como funciona

- Uma requisição de metadados + um `batchGet` por planilha, todas em paralelo, pela
  **Sheets API v4**.
- As abas são identificadas por **gid**, não por nome: as planilhas do escritório são
  renomeadas com frequência (a aba `ITIV/Rec J.` virou `ITIV` durante a construção deste
  painel) e um range por nome quebraria silenciosamente. O painel lê o nome atual de cada
  gid antes de montar o range.
- Toda leitura de coluna é por nome de cabeçalho, com fallback pela letra da coluna.
- Atualização automática a cada 5 minutos. Arquivo único `index.html`, sem build.

## Consultor IA

Campo de pergunta em linguagem natural sobre os números do painel, no topo da página.

- **Modelo**: Claude Sonnet 5.
- **Chave da API**: cifrada dentro do **cofre do painel** (AES-256-GCM, PBKDF2 com
  310.000 iterações), junto com os IDs das planilhas. Quem faz login com Administração
  ou Lideranças libera a chave; o consultor então funciona em **celular, iPad e
  computador** sem colar nada. Nunca aparece em claro no repositório, e a única saída
  é a chamada direta à API do Claude. Colar a chave manualmente (guardada no
  `localStorage` daquele navegador) segue valendo como alternativa.
- **Não estima**: toda resposta passa por ferramentas que leem os dados. O modelo só
  pode responder com o que a ferramenta devolveu.

### Ferramentas do consultor

| Ferramenta | Para que serve |
|---|---|
| `consultar_metricas` | Snapshot conferido de todas as áreas (agregados), incluindo o recorte `gestao_executiva`: North Star, lockup por trecho, collection rate, write-off e pipeline não trabalhado |
| `listar_alertas` | Alertas com diagnóstico, causa, solução, responsável e prazo |
| `listar_registros` | **Registros individuais** — processo a processo, cliente a cliente. Responde "quais", "me dê a lista", "quem são" |
| `buscar` | Procura um cliente, processo, réu ou vara em todas as fontes de uma vez |
| `explorar_planilhas` | Lista as 14 planilhas do escritório e as abas de cada uma |
| `consultar_planilha` | Lê qualquer aba sob demanda, inclusive das planilhas que não alimentam o painel |

### Recortes de `listar_registros`

| Fonte | Recortes |
|---|---|
| `alvaras` | todos, sem_rateio, pendente_repasse_bm, pendente_repasse_cliente, fora_do_fluxo, maiores_valores |
| `crm_analises` | todas, viaveis_sem_agenda, pendentes, inviaveis |
| `crm_atendimentos` | todos, sem_desfecho, no_show, ganhos, perdidos |
| `iniciais` | todas, vencidas_sem_protocolo, fora_do_prazo, no_prazo |
| `onboarding` | todas, sem_registro, nao_realizada |
| `audiencias` | todas, futuras, sem_ciencia, proximos_7_dias |
| `diligencias` | todas, abertas, sem_contato, mais_de_15_dias, cumpridas |
| `execucoes` | todos, prazo_vencido, vencendo_7_dias, marcados_diligenciar, itiv_pagamento_vencido, maiores_valores |

### As 14 planilhas ao alcance do consultor

Seis alimentam o painel visual (Alvarás, CRM, Controladoria, Diligências, Execuções e
Base de Clientes). As outras oito são lidas **sob demanda**, só quando a pergunta exige:
Fluxo Financeiro (DRE), Fluxo de Honorários, Fluxo de Clientes, Metas e Resultados —
Processual, KPIs do Comercial, Clientes e Processos, Processos [DataJud + DJEN] e Metas
Gerais.

Abas que não são tabelas (o DRE, por exemplo, é uma matriz) são detectadas e entregues
em formato adequado, com aviso — a leitura não quebra nem inventa colunas.
- **Cuidados embutidos no prompt**: distinguir receita potencial estimada de dinheiro
  parado, nunca somar valores de alertas diferentes (carteiras se sobrepõem) e alertar
  que o win rate considera só os atendimentos com desfecho registrado.

Cinco perguntas sugeridas ficam disponíveis em um clique, entre elas *"Quais são minhas
3 prioridades desta semana?"* e *"Se eu só puder resolver um problema este mês, qual
deve ser?"*.

## Camada de gestão executiva

Incorporada a partir da prática corrente de managing partners e de general counsel:

### North Star
Uma única métrica em destaque: **quanto das análises viáveis vira contrato**. É
*behavioral* (mede comportamento, não resultado), *controlável* (o time move na semana)
e *preditiva* (antecede a receita em um ciclo). Faturamento é resultado — se virasse
North Star, o painel só olharia para trás.

### Ciclo de Caixa (Lockup)
Dias entre o trabalho feito e o dinheiro no caixa, quebrado por trecho e com dono
nomeado. A referência é a mediana de mercado do **Clio Legal Trends Report 2025**:
43 dias até faturar + 32 até receber = **75 dias**. Reduzir o ciclo libera capital de
giro que já é do escritório — sem vender mais nada.

Também mede **collection rate** (referência de mercado: 93%) e **write-off** (média de
14% do trabalho realizado).

### Indicadores antecedentes × de resultado
Separação explícita entre o que se move agora (leading) e o que apenas se confere
(lagging). Cada antecedente traz alvo, estado e o efeito que produz. Escolher um
indicador de resultado como bússola "starva o presente de feedback".

### Cadência de gestão
Semanal (operação), mensal (resultado) e trimestral (estrutura), cada faixa com pauta
própria e a contagem de alertas que lhe pertence. É o ritmo que separa gerenciar de
apagar incêndio.

> A prática de mercado recomenda manter **3 a 5 métricas com forcing function** em vez
> de um mural de indicadores. Por isso o painel destaca uma North Star e dois números
> de decisão, deixando o resto como consulta.
