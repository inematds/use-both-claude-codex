# O vídeo "Use Both" — resumo e o que ele acrescenta ao kit

Análise feita em 27/09/2026 a partir da transcrição (versão dublada em alemão) do vídeo de Mark Kashef que este kit acompanha.
É um resumo com comentários do INEMA, não uma tradução. As opiniões sobre qual modelo é melhor são do autor, **sem benchmark**.

## Resumo em 10 linhas

- **Tese:** Opus 5.5 e GPT-6 Astra se completam. O Claude é otimista e bom para planejar; o Codex é pessimista e bom para achar falhas no plano.
- **Por que funciona:** o modelo que escreveu um plano, ainda mais na mesma sessão, tende a aprová-lo. Um modelo diferente, ou uma sessão nova, dá outro resultado.
- **Não precisa de dois planos Max:** o plano de 20 dólares do Codex já basta para revisão e imagens.
- **Três formas de ligar os dois:**
  1. o plugin oficial do Codex para o Claude Code;
  2. uma CLI chamando a outra (o Claude roda `codex exec` sem interface; o Codex roda o `claude`), com a resposta salva num `.md`;
  3. um pull request que o outro modelo revisa.
- **Os seis níveis:** discutir o plano; gerar imagem; dividir a execução (ex.: 70% Claude, 30% Codex); computer use; meta (`/goal`); handoff/prime.
- **Imagem:** em vez de pagar API do Gemini ou da OpenAI, usa a geração de imagem da assinatura do Codex e devolve o arquivo ao Claude.
- **Meta:** o Codex persegue um `/goal` de forma exaustiva. O autor relata execuções de 18 a 30 horas, 3 a 5 vezes mais tempo e muito mais tokens. Por isso sugere um prazo: "se travar depois de 2 horas, pare e diga por quê", ou "passe o problema ao Claude".
- **Handoff:** substitui a pilha de `plan.md` desatualizados. `/handoff` no fim da sessão, `/prime` na próxima, em qualquer modelo, inclusive local ou open source.
- **Tabela do autor:** planejar → Claude. Quebrar o plano em tarefas → Codex. Imagens para cortar custo → Codex. Construir e publicar → Opus. Revisar pronto → várias rodadas com o Codex. Computer use → Codex. Problema complexo, com tokens de sobra → Codex.
- **Oferta:** o guia, os prompts e as skills `prime` e `handoff` são grátis. São exatamente este kit.

## O que o vídeo traz e o kit não detalha

| Tema | Vídeo | Kit | Como aproveitar no INEMA |
|---|---|---|---|
| Ligação por CLI | Um chama o outro sem plugin, com esforço médio | Rota B de `docs/setup.md`, só leitura | Comandos testáveis abaixo |
| "Discutir até concordarem" | Loop livre até os dois não terem mais nada a acrescentar | No máximo 2 rodadas; concordar não é prova | Seguir o kit. Loop longo e automatizado é trabalho do `claudex:plan` |
| O loop deixou o claudex obsoleto? | O autor diz que antes usava o loop "Claude-X" e hooks, e que hoje basta pedir | Não comenta | Para plano grande, o claudex ainda ganha: 3 ângulos, estado, rollback. Para plano curto, basta o prompt |
| Quebrar o plano em tarefas | Codex | Não aparece na tabela | Acrescentar à tabela de rotas |
| 70/30 na execução | Proporção sugerida | "Dividir o trabalho", sem proporção | É heurística, não regra |
| Custo do `/goal` | 3–5× mais tempo e tokens no Codex; 18–30 h | "Frase de prazo não é limite real" | Usar um limite de verdade: `timeout`, orçamento, número de rodadas |
| Computer use e credenciais | O Codex é "mais relaxado" para logar e copiar credenciais; o Claude recusa | Você digita senha e MFA; nunca usar outro modelo para contornar uma recusa | **Seguir o kit.** Trocar de modelo para escapar de uma recusa de segurança não vale no INEMA |

## Comandos para o nosso ambiente

Conferidos com `codex-cli 0.157.1` e o `claude --help` local em 27/09/2026. Cada chamada consome a **assinatura** de quem é chamado, sem API.

```bash
# Claude → Codex: revisão só leitura, resposta em arquivo
codex exec --sandbox read-only -c model_reasoning_effort=medium \
  --output-last-message review.md \
  'Leia plan-v1.md. Aponte falhas de correção e testes faltando, com evidência, impacto e a menor correção. Não altere arquivos.'

# Codex → Claude: o caminho inverso
claude -p --model opus --effort medium \
  'Leia plan-v1.md e revise só lendo: falhas, suposições e testes faltando. Não altere arquivos.' > review-claude.md

# Plugin oficial dentro do Claude Code (opcional)
/plugin marketplace add openai/codex-plugin-cc
/plugin install codex@openai-codex
/codex:review --base main
```

## Imagem: o caso do INEMA

O nível 2 do vídeo já é o que o INEMA faz por script. Capa do catálogo, banner dos guias, cenas dos cursos v6 e miniaturas do eventos chamam `codex exec` com a geração de imagem do Codex, e caem para o FLUX.2 klein local se o Codex falhar. A diferença está na forma: o vídeo propõe pedir peça por peça, conversando; nós geramos em lote, com fallback. Do kit, vale adotar três coisas: checar a ferramenta e a cobrança antes, conferir a imagem no tamanho em que será usada e salvar as revisões como arquivos separados. Desde 26/09/2026, essa geração só roda com autorização explícita.

## Onde isso foi aplicado

- Área **Codex + Claude** do INEMA Eventos: https://eventos.inema.pro/claude-codex/ (seções "Usar os dois juntos" e "Como tudo se integra").
- Kit **agente-claude-codex**: handoff com histórico, prime endurecido, instalador em modo prévia e prompts "usar os dois".
