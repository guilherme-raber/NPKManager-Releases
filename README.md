# NPKManager

**Versão disponível: 1.7.0 · executável Windows x64**

O NPKManager ajuda operadores a analisar equipamentos MikroTik RouterOS, revisar atualizações, salvar backups de configuração e executar somente as operações aprovadas.

- [Baixar NPKManager-1.7.0.exe](https://github.com/guilherme-raber/NPKManager-Releases/releases/download/v1.7.0/NPKManager-1.7.0.exe)
- [Ver a release mais recente](https://github.com/guilherme-raber/NPKManager-Releases/releases/latest)
- [Instalação e uso](INSTALACAO-E-USO.md)
- [Histórico da versão 1.7.0](CHANGELOG.md)

O executável não exige Python instalado. A distribuição atual é destinada a Windows x64. Foi testada no ambiente Windows disponível; não foi validada em uma instalação Windows limpa. Não declaramos uma versão mínima oficial nem compatibilidade universal.

## O que o programa faz

O fluxo separa análise e manutenção: o operador informa alvos e acesso, analisa os equipamentos, revisa os resultados, seleciona os elegíveis, salva os exports de configuração quando aplicável, confere o plano e aprova explicitamente a atualização. O programa apresenta o resultado e gera relatórios na pasta configurada.

O NPKManager trabalha com RouterOS v6 ou v7 mantendo a mesma versão principal instalada. Não migra automaticamente de v6 para v7 e não faz downgrade. Uma verificação de segurança ou outro precheck pode bloquear a operação até que o operador revise o equipamento.

Em MikroTik CHR, RouterBOOT é não aplicável. No modo combinado, o programa atualiza RouterOS sem tentar atualizar RouterBOOT nem fazer um segundo reboot por esse motivo. As demais verificações continuam ativas.

Para atualizações RouterOS, é necessário configurar um servidor HTTP ou HTTPS acessível com pacotes NPK oficiais compatíveis com a versão e arquitetura dos equipamentos. A operação somente de RouterBOOT não precisa desse servidor.

O indicador `flagged=yes` exige revisão manual e bloqueia a atualização automática; por si só, não prova invasão. Em alguns firmwares legados, o campo não está disponível para consulta. Esse estado não significa que o equipamento esteja seguro, e as demais verificações continuam necessárias.

Exports `.rsc` podem conter informações sensíveis. Guarde backups e relatórios em uma pasta com acesso restrito.
