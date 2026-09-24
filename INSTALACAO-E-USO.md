# Instalação e uso — NPKManager 1.7.0

## Baixar e iniciar

1. Baixe [`NPKManager-1.7.0.exe`](https://github.com/guilherme-raber/NPKManager-Releases/releases/download/v1.7.0/NPKManager-1.7.0.exe) pela página oficial de [Releases](https://github.com/guilherme-raber/NPKManager-Releases/releases).
2. Salve o executável em uma pasta de sua preferência e abra-o no Windows x64.
3. Não é necessário instalar Python para usar esse executável.

A versão distribuída é destinada a Windows x64 e foi validada no ambiente Windows disponível. O teste em uma instalação Windows limpa não foi realizado; não há uma versão mínima oficial do Windows declarada. Se o sistema bloquear a execução, siga as políticas de segurança da sua organização e confirme a origem do arquivo antes de prosseguir.

**Verificação do arquivo**

```text
Arquivo: NPKManager-1.7.0.exe
SHA-256: 94C3A0708BC763448160218726766E76CA06D1AAAEF3ED61DA85035E7C59D041
```

## Antes de começar

- Tenha autorização para administrar os equipamentos e programe uma janela de manutenção.
- Confirme o acesso de rede às portas Winbox e SSH configuradas e use credenciais autorizadas.
- Para atualizar RouterOS, prepare um servidor HTTP ou HTTPS acessível com os pacotes NPK oficiais correspondentes à arquitetura e à versão pretendida. A operação somente de RouterBOOT não precisa de servidor NPK.
- Escolha uma pasta protegida para backups e relatórios. Exports `.rsc` podem conter informações sensíveis.

## Analisar e atualizar

1. Na tela de conexão, informe os alvos, as portas Winbox e SSH, o usuário e a senha. Você pode digitar uma rede ou importar uma lista de alvos por arquivo.
2. Para operações RouterOS, informe o endereço HTTP/HTTPS do servidor de pacotes. Escolha também a pasta de relatórios.
3. Selecione o escopo: RouterOS + RouterBOOT, somente RouterOS ou somente RouterBOOT.
4. Clique em **Analisar rede**. A análise consulta os equipamentos e prepara um plano; não altera configurações nem reinicia os roteadores.
5. Na lista de resultados, revise versões, pacotes, espaço disponível, verificações de segurança e eventuais bloqueios. Selecione somente os equipamentos desejados.
6. Salve os exports `.rsc` na pasta escolhida e confirme que os backups foram concluídos.
7. Confira novamente o plano de cada equipamento e aprove explicitamente a atualização. Não prossiga se o resultado ou o equipamento não forem os esperados.
8. Acompanhe o resultado e consulte os relatórios na pasta configurada. Nos modos que não aguardam reconexão, faça uma nova análise para verificar a versão instalada.

## Regras de versão e CHR

O NPKManager mantém a linha principal instalada: RouterOS v6 continua em v6 e RouterOS v7 continua em v7. Não há migração automática v6 → v7 nem downgrade automático.

No MikroTik CHR, RouterBOOT é apresentado como **N/A (CHR)**. Se for escolhido RouterOS + RouterBOOT, somente RouterOS é considerado; não há upgrade nem segundo reboot de RouterBOOT. Se for escolhido somente RouterBOOT, o programa informa que não se aplica e não inicia uma operação.

## Segurança e resultados

- `flagged=yes` é um indicador que exige revisão manual e bloqueia a atualização automática. Não é, por si só, prova de invasão, e o programa não limpa esse indicador.
- Em firmware legado que não apresenta o campo, o estado pode aparecer como `FLAGGED_FIELD_UNAVAILABLE`. Isso significa que o campo não pôde ser verificado; não equivale a `SAFE`. O fluxo só pode prosseguir se as outras verificações forem aprovadas.
- Uma resposta inesperada, inválida ou ambígua, falta de permissão ou falha de consulta pode bloquear o equipamento. Outras verificações de segurança e prechecks também podem impedir uma atualização.
- O arquivo `.rsc` é um export textual, não um backup completo do equipamento. O NPKManager não restaura configurações automaticamente.

Restrinja o acesso às pastas de backup e relatório. Não publique nem envie exports em canais abertos.
