# UTB Prova

Este repositório prova que cada decisão do comitê UTB existia antes do resultado ser conhecido.

## Como funciona

1. Quando o comitê decide, geramos o SHA-256 do arquivo da decisão. Cada linha de `hashes/AAAA-MM.csv` é a impressão digital de uma decisão, publicada antes do resultado.
2. O conteúdo da decisão fica guardado. Aqui aparece só o hash, com um identificador neutro (exemplo: `UTB-2026-10-15-01`).
3. O mesmo hash é registrado no OpenTimestamps, que ancora a prova no blockchain do Bitcoin. A prova fica em `ots/`.
4. Depois do resultado, o arquivo original é revelado em `reveal/`. Qualquer pessoa recalcula o hash e compara.

A data vem de duas fontes independentes: o horário do commit no GitHub e a prova do OpenTimestamps.

## Como verificar

As provas do OpenTimestamps estão em `ots/` como texto base64 (`.ots.b64`), porque o envio usado aqui só aceita texto. Decodifique antes de verificar.

```
# 1. calcule o hash do arquivo revelado
sha256sum reveal/UTB-2026-10-16-01.md

# 2. compare com a linha publicada antes
grep UTB-2026-10-16-01 hashes/2026-10.csv

# 3. confira a prova no Bitcoin (pip install opentimestamps-client)
base64 -d ots/UTB-2026-10-16-01.sha256.ots.b64 > UTB-2026-10-16-01.sha256.ots
ots verify -f ots/UTB-2026-10-16-01.sha256 UTB-2026-10-16-01.sha256.ots
```

O carimbo `*.sha256.ots` é feito sobre o arquivo `ots/<codigo>.sha256`, que contém o hash seguido de uma quebra de linha (65 bytes, igual à saída de `echo <hash>`). Para `UTB-2026-10-15-01` o carimbo `UTB-2026-10-15-01.md.ots.b64` foi feito direto sobre o arquivo da decisão (`ots verify -f reveal/UTB-2026-10-15-01.md UTB-2026-10-15-01.md.ots`).

Sem um nó Bitcoin local, use `ots --no-bitcoin verify ...` para conferir o caminho até o bloco, ou envie o arquivo e o `.ots` em https://opentimestamps.org.

Se os hashes forem iguais, o arquivo não mudou nenhum caractere desde a publicação.

## Dados do site (`data/`)

O site UTB lê estes arquivos diretamente daqui. Se a leitura falhar, o site mostra a última versão embutida nele.

* `data/lamina.json`: lâmina de resultados em % e base 100 (série, meses, estatísticas e setores). Atualizada no último dia útil de cada mês.
* `data/comite.json`: decisões do comitê vindas do radar que terminaram sem entrada (NAO_COMPRAR e ESPERAR), com data, mercado, ticker, veredito, confiança e notas de 1 a 5. Cada decisão só aparece 2 pregões depois. Compras aparecem pela prova acima (hash no dia, revelação depois). Revisões de carteira não entram.
* `data/revealed.json`: lista das operações carimbadas, com o hash e, depois da revelação, ticker e lado.

Nenhum destes arquivos traz valores em dinheiro, quantidades, contas ou nome de corretora.

Este repositório não contém posições, valores nem dados de corretora.
