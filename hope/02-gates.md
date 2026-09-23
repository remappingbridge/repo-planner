# HOPE gate plan

Status geral: **IN PROGRESS — HOPE-00/01/02/08/24/09/25 ACCEPTED; HOPE-06 ACTIVE**.

## Ordem oficial de execução

A numeração identifica o gate; a **ordem de execução oficial** é a ordem do fluxo fornecido para o programa:

~~~text
HOPE-00
HOPE-01
HOPE-02
HOPE-08
HOPE-24
HOPE-09
HOPE-25
HOPE-06
HOPE-26
HOPE-07
HOPE-27
HOPE-03
HOPE-28
HOPE-10
HOPE-29
HOPE-11
HOPE-14
HOPE-12
HOPE-13
HOPE-15
HOPE-16
HOPE-17
HOPE-18
HOPE-19
HOPE-20
HOPE-21
HOPE-22
HOPE-04
HOPE-05
HOPE-30
HOPE-23
HOPE-31
~~~

Somente o primeiro gate não aceito da sequência pode ser iniciado.

## Regra universal de todos os gates

Em todo gate, inclusive gates de Help e HOPE-00:

- **adaptar/reaproveitar = substituir in-place**: pode reutilizar lógica G06, mas o layout antigo correspondente não pode continuar como fallback, alternativa ou tela paralela;
- toda rota equivalente ao ponto de fluxo substituído deve mostrar exclusivamente o layout Mouse UI v1 do gate atual;
- layout da tela introduzida deve corresponder à referência Mouse UI v1;
- região de dicas deve usar a cor correta;
- pixels/backgrounds devem estar nas posições corretas;
- pareamento de Mouse deve continuar funcionando;
- movimento do Mouse deve continuar funcionando;
- cliques devem continuar funcionando;
- regressões de gates HOPE já aceitos devem continuar consistentes;
- aceitação física pelo operador é obrigatória.

---

## HOPE-00 — exact G06 baseline

**Status:** ACCEPTED / PROMOTED TO MAIN `16b9a3a2f164f08e3a9c25fc39a4767b04f48f39`.

**Objetivo:** copiar exatamente BLU2USB G06 aceito para `remappingbridge/remappingbridge` sem quebrar nada.

**Fonte:** `remappingbridge/blu2usb@7eee024ad4ee726c5a85ffa2f32b9f47187878af`.

**Não fazer neste gate:** nenhuma tela Mouse UI v1; nenhuma melhoria; nenhum refactor; nenhuma mudança de arquitetura.

**Aceitação:** árvore funcional equivalente à baseline G06, build/UF2 reproduzível e teste físico regressivo do comportamento G06.

---

## HOPE-01 — searching-first

**Status:** ACCEPTED / PROMOTED TO MAIN `4b0ebe933721895488a38f84c03f009c0d30cb32`.

**Objetivo:** substituir a apresentação inicial herdada pela tela `searching-first` Mouse UI v1.

**Adaptar antigo para novo:** transformar in-place o ponto de fluxo/tela antiga `LEARN THE KEYS` usada no primeiro boot. Reutilizar pareamento automático e infraestrutura existente por baixo, mas **não manter o layout antigo em nenhuma condição desse ponto de fluxo**.

**Remover antigo:** remover a apresentação visual antiga desse ponto do fluxo. Não criar `searching-first` em paralelo deixando `LEARN THE KEYS` como fallback.

---

## HOPE-02 — first-mouse-connected

**Status:** ACCEPTED / PROMOTED TO MAIN `d13f905fc2a817f464f3683edd4bf689247c30ed`.

**Objetivo:** implementar `first-mouse-connected` como feedback de sucesso de `searching-first`.

---

## HOPE-08 — home-searching

**Status:** ACCEPTED / PROMOTED TO MAIN `c88ebacdeb8265c9b8bd82882f97d9b8c960493a`.

**Objetivo:** implementar `home-searching` como estado da HOME e integrá-la ao fluxo já aceito.

---

## HOPE-24 — home-searching-help

**Status:** ACCEPTED / PROMOTED TO MAIN `aa6abb2294248aa8eabc4918eae1bdcfef26a959`.

**Objetivo:** implementar `home-searching-help` conforme Mouse UI v1.

---

## HOPE-09 — home-retry

**Status:** ACCEPTED / PROMOTED TO MAIN `b883353bb654826d4c960513e72182d2b35b4c6a`.

**Objetivo:** implementar `home-retry` como estado da HOME e integrá-la ao fluxo já aceito.

---

## HOPE-25 — home-retry-help

**Status:** ACCEPTED / PROMOTED TO MAIN `def2743022c3b522190eac06b97af0d4148348bd`.

**Objetivo:** implementar `home-retry-help` conforme Mouse UI v1.

---

## HOPE-06 — pair-new

**Status:** REWORK — FIRST PHYSICAL CANDIDATE FAILED; BTSTACK DUAL-SESSION CAPACITY FIX IN PROGRESS.

**Objetivo:** implementar `pair-new` conforme Mouse UI v1.

**Adaptar antigo para novo:** substituir in-place o fluxo antigo `HOME > MOUSE OPTIONS > PAIR DEVICE`, reaproveitando apenas sua lógica funcional; as telas/layouts antigos correspondentes não permanecem como fallback.

**Remover antigo completamente:** remover as telas antigas subsequentes desse fluxo antigo.

---

## HOPE-26 — help-pair-new

**Objetivo:** implementar `help-pair-new` conforme Mouse UI v1.

---

## HOPE-07 — retry-pair-new

**Objetivo:** implementar `retry-pair-new` conforme Mouse UI v1.

---

## HOPE-27 — help-retry-pair-new

**Objetivo:** implementar `help-retry-pair-new` conforme Mouse UI v1.

---

## HOPE-03 — home-connected

**Objetivo:** implementar `home-connected` como estado da HOME.

**Adaptar antigo para novo:** substituir in-place a HOME antiga por `home-connected`, reaproveitando somente lógica/estado necessários; a HOME antiga não permanece como alternativa.

---

## HOPE-28 — help-home-connected

**Objetivo:** implementar `help-home-connected` conforme Mouse UI v1.

---

## HOPE-10 — remapper-options

**Objetivo:** substituir in-place o antigo `HOME > MOUSE OPTIONS` por `remapper-options`, mantendo suas funções úteis por baixo e exibindo somente o layout/texto Mouse UI v1.

---

## HOPE-29 — help-remapper-options

**Objetivo:** implementar `help-remapper-options` conforme Mouse UI v1.

---

## HOPE-11 — passthrough-active

**Objetivo:** preservar a função do antigo `HOME > MOUSE OPTIONS > PASSTHROUGH` e aplicar layout/texto Mouse UI v1.

---

## HOPE-14 — passthrough-not-active

**Objetivo:** preservar a função do antigo `HOME > MOUSE OPTIONS > PASSTHROUGH` e aplicar layout/texto Mouse UI v1.

---

## HOPE-12 — standard-not-active

**Objetivo:** preservar a função do antigo `HOME > MOUSE OPTIONS > DEFAULT REMAP` e aplicar layout/texto Mouse UI v1.

---

## HOPE-13 — standard-active

**Objetivo:** preservar a função do antigo `HOME > MOUSE OPTIONS > DEFAULT REMAP` e aplicar layout/texto Mouse UI v1.

---

## HOPE-15 — escape-not-active

**Objetivo:** preservar a função do antigo `HOME > MOUSE OPTIONS > ESCAPE REMAP` e aplicar layout/texto Mouse UI v1.

---

## HOPE-16 — escape-active

**Objetivo:** preservar a função do antigo `HOME > MOUSE OPTIONS > ESCAPE REMAP` e aplicar layout/texto Mouse UI v1.

---

## HOPE-17 — custom-edit

**Objetivo:** preservar a função do antigo `HOME > MOUSE OPTIONS > CUSTOM REMAP (EDIT CUSTOM REMAP)` e aplicar layout/texto Mouse UI v1.

---

## HOPE-18 — left

**Objetivo:** preservar a função antiga de edição `LEFT IS... / LEFT WILL BECOME` e aplicar a tela `left` Mouse UI v1.

---

## HOPE-19 — right

**Objetivo:** preservar a função antiga de edição `RIGHT IS... / RIGHT WILL BECOME` e aplicar a tela `right` Mouse UI v1.

---

## HOPE-20 — middle

**Objetivo:** preservar a função antiga de edição `MIDDLE IS... / MIDDLE WILL BECOME` e aplicar a tela `middle` Mouse UI v1.

---

## HOPE-21 — forward

**Objetivo:** preservar a função antiga de edição `FORWARD IS... / FORWARD WILL BECOME` e aplicar a tela `forward` Mouse UI v1.

---

## HOPE-22 — backward

**Objetivo:** preservar a função antiga de edição `BACKWARD IS... / BACKWARD WILL BECOME` e aplicar a tela `backward` Mouse UI v1.

---

## HOPE-04 — saved-devices

**Objetivo:** implementar `saved-devices`.

**Adaptar antigo para novo:** substituir in-place `HOME > OTHER OPTIONS > SAVED DEVICES` pela nova `saved-devices`, reutilizando apenas a funcionalidade interna necessária.

**Remover antigo completamente:** remover `PAIR KEYBOARD`, `PAIR COMPOSITE` e telas subsequentes desses fluxos, se existirem.

---

## HOPE-05 — remove-this

**Objetivo:** implementar `remove-this` conforme Mouse UI v1.

---

## HOPE-30 — help-remove-this

**Objetivo:** implementar `help-remove-this` conforme Mouse UI v1.

---

## HOPE-23 — learn-the-keys

**Objetivo:** implementar `learn-the-keys` conforme Mouse UI v1.

---

## HOPE-31 — final inventory and documentation

**Objetivo:** garantir exatamente **30 telas novas/canônicas** com regras bem definidas e nenhuma tela antiga solta, escondida ou alcançável fora do fluxo.

**Inventário esperado:** os 30 gates de tela HOPE-01..30 listados neste plano.

**Documentação final:** registrar arquitetura efetivamente resultante e regras em `remappingbridge/remappingbridge/docs`, descrevendo a implementação simples realmente existente, sem inventar contratos/Core que o programa excluiu.

**Aceitação física:** executar matriz final completa das 30 telas e regressão de pareamento, movimento, cliques, perfis, persistência/retorno aplicável e navegação.
