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
- **Chave da API**: colada pelo próprio usuário e guardada **apenas no `localStorage`
  do navegador**. Nunca vai para o repositório nem para servidor algum — a única
  saída é a chamada direta à API do Claude.
- **Não estima**: toda pergunta quantitativa passa por uma de duas ferramentas —
  `consultar_metricas` (snapshot conferido de todas as áreas) e `listar_alertas`
  (alertas com diagnóstico, causa, solução, responsável e prazo). O modelo só pode
  responder com o que a ferramenta devolveu.
- **Cuidados embutidos no prompt**: distinguir receita potencial estimada de dinheiro
  parado, nunca somar valores de alertas diferentes (carteiras se sobrepõem) e alertar
  que o win rate considera só os atendimentos com desfecho registrado.

Cinco perguntas sugeridas ficam disponíveis em um clique, entre elas *"Quais são minhas
3 prioridades desta semana?"* e *"Se eu só puder resolver um problema este mês, qual
deve ser?"*.
