# Instalação e uso

Este guia descreve o fluxo para operadores. Baixe o aplicativo apenas pela página [GitHub Releases do NPKManager-Releases](https://github.com/guilherme-raber/NPKManager-Releases/releases). A release indicará quais sistemas e arquivos estão disponíveis.

## Antes de começar

- Confirme que você tem autorização para administrar os equipamentos e agende uma janela de manutenção.
- Verifique o acesso de rede aos equipamentos: a porta Winbox configurada é testada antes da tentativa SSH.
- Tenha credenciais SSH autorizadas e acesso à pasta do servidor de pacotes, quando a operação incluir RouterOS.
- Prepare uma pasta local protegida para os exports .rsc e relatórios.

No Windows, execute o arquivo independente da release. Ele não requer instalação separada do Python. Outros sistemas só são suportados quando listados nos arquivos da release.

## Preparar o servidor de pacotes

Para uma operação RouterOS, configure uma pasta HTTP ou HTTPS acessível que permita listar os arquivos NPK. Use pacotes oficiais MikroTik, com nome e conteúdo originais, compatíveis com a versão e arquitetura do equipamento. Mantenha os pacotes necessários diretamente na pasta publicada.

HTTPS é recomendado. HTTP não protege a conexão com TLS. Não substitua o conteúdo de um NPK mantendo o mesmo nome durante uma manutenção. A operação Somente RouterBOOT não precisa do servidor de NPK.

## Executar uma manutenção

1. Informe os endereços ou alvos autorizados, as portas Winbox e SSH, o usuário e a senha.
2. Configure o endereço HTTP/HTTPS do servidor de pacotes para operações RouterOS e escolha onde salvar relatórios.
3. Escolha o escopo: RouterOS + RouterBOOT, Somente RouterOS ou Somente RouterBOOT.
4. Clique em Analisar rede. A análise coleta informações e prepara um plano; não altera configurações nem reinicia o equipamento.
5. Revise os resultados. Confira cada equipamento, a versão e os pacotes propostos. Resolva bloqueios antes de prosseguir.
6. Selecione apenas os equipamentos que deseja manter. Salve os exports .rsc na pasta local e confirme que os backups foram concluídos.
7. Revise novamente o plano, aprove explicitamente a atualização e confirme a operação.
8. Aguarde o resultado e consulte os relatórios. Em modos que não aguardam a reconexão, faça uma nova análise para verificar a versão instalada.

O modo combinado pode realizar etapas de RouterOS e RouterBOOT conforme o equipamento e o plano. RouterBOOT não se aplica ao MikroTik CHR. Não desligue nem desconecte um equipamento durante uma atualização ou reinicialização.

## Segurança e resultados

- flagged=yes: requer revisão manual e bloqueia a atualização automática. O indicador é um alerta, não uma prova conclusiva de invasão. O aplicativo não tenta limpar ou corrigir esse estado.
- FLAGGED_FIELD_UNAVAILABLE: algumas respostas de firmware legado não apresentam o campo flagged. O estado permanece não verificado e não significa que o equipamento está seguro; o fluxo pode prosseguir somente se as outras verificações forem aprovadas.
- Resposta ausente inesperada, inválida, ambígua ou erro de consulta: pode deixar o estado de segurança desconhecido ou inválido e bloquear a operação.
- RouterOS 6 e 7: o aplicativo não realiza migração automática entre versões principais nem downgrade.
- CHR: RouterBOOT é indicado como não aplicável; as verificações de segurança e as demais validações de RouterOS continuam ativas.

## Proteger backups e relatórios

O arquivo .rsc é um export textual de configuração, não um backup completo do equipamento. Pode conter informações sensíveis. Restrinja o acesso à pasta, não publique nem envie os arquivos em chamados abertos, e siga o procedimento de proteção de dados da sua organização.

Leia as mensagens do aplicativo e confirme que o backup está disponível antes de aprovar a atualização. O NPKManager não restaura configurações automaticamente.
