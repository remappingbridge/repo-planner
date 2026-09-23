# HOPE — Remapping Bridge incremental UI migration

Status: **HOPE-00 IN PROGRESS / PHYSICAL ACCEPTANCE PENDING**.

Este namespace é a fonte de planejamento do programa HOPE do repositório `remappingbridge/remappingbridge`.

## Intenção

1. começar do firmware funcional de `remappingbridge/blu2usb` no G06 aceito;
2. no HOPE-00, transferir essa baseline para `remappingbridge/remappingbridge` sem alterar comportamento;
3. depois substituir **uma tela por gate**, preservando a arquitetura funcional de BLU2USB;
4. usar `remappingbridge/mouse-ui` UI Layout 1.0 apenas como autoridade para regras observáveis das telas/fluxos que forem sendo introduzidos;
5. manter pareamento, movimento e cliques do Mouse funcionando a cada etapa;
6. exigir teste físico em **todo gate** antes de aceitação.

## O que HOPE deliberadamente não é

HOPE não replica:

- contratos UI↔Core;
- arquitetura de `mouse-core`;
- arquitetura interna de `mouse-ui`;
- pareamento Bluetooth Keyboard;
- pareamento Bluetooth Composite;
- processo MUX/UIC/MCORE;
- integração sofisticada criada apenas por abstração/precisão arquitetural.

A integração deve ser a menor adaptação necessária entre funções que já funcionam no BLU2USB G06 e as regras visuais/de interação da tela sendo migrada.

## Estado

- preparação documental: **COMPLETE**;
- código migrado para `remappingbridge/remappingbridge`: **sim, em `hope/hope-00-exact-g06-baseline`**;
- HOPE-00: **IN PROGRESS — exact G06 candidate built from pinned tree**;
- gates aceitos: **nenhum**.

Leia na ordem:

1. `00-authority-scope-and-precedence.md`;
2. `01-baselines-and-provenance.md`;
3. `02-gates.md`;
4. `03-execution-rules.md`;
5. `04-physical-acceptance.md`;
6. `05-preflight.md`.
