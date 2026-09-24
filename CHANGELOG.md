# Histórico de versões

## 1.6.1

- Corrigidos falsos bloqueios em firmware RouterOS legado cujo formato válido não apresenta o campo flagged.
- O estado FLAGGED_FIELD_UNAVAILABLE identifica claramente que a verificação não foi possível; ele é diferente de SAFE.
- flagged=yes continua exigindo revisão manual e bloqueando a atualização automática.
- A seleção de vários equipamentos verifica explicitamente o estado de segurança além do status de atualização.

## 1.6.0

- flagged=yes passou a exigir revisão manual. O indicador é tratado como alerta, não como prova de invasão.
- A verificação do campo flagged passou a rejeitar respostas ausentes, inválidas, ambíguas ou erros de consulta em firmware no qual o campo é esperado.
- O estado e as evidências de segurança passaram a ser apresentados com mais clareza durante a operação.

## 1.5.1

- Corrigida a seleção de pacotes RouterOS para CHR com arquitetura x86_64.
- Melhorado o tratamento de pacotes adicionais sem arquitetura explícita no nome.
