# NPKManager

O NPKManager ajuda operadores a analisar equipamentos MikroTik RouterOS, revisar operações planejadas, salvar exports de configuração e executar somente a manutenção aprovada.

**Versão disponível: 1.8.0 · Windows x64, Linux x86_64 e macOS (Apple Silicon e Intel)**

- [Baixar a versão mais recente](https://github.com/guilherme-raber/NPKManager-Releases/releases/latest)
- [Instalação e guia de operação](INSTALACAO-E-USO.md)
- [Preparação do servidor de pacotes NPK](SERVIDOR-NPK.md)
- [Histórico de versões](CHANGELOG.md)

Na seção **Assets** da release, escolha o pacote do seu sistema. Cada pacote tem um arquivo `.sha256` correspondente para conferência de integridade. Os arquivos **Source code (zip)** e **Source code (tar.gz)** são gerados automaticamente pelo GitHub e correspondem somente a este repositório público de distribuição e documentação; eles não contêm o código-fonte da aplicação NPKManager.

Os pacotes são gerados e validados em runners nativos: Windows x64, Linux x86_64, macOS arm64 e macOS x86_64. A matriz executa 230 testes por sistema e um autoteste do aplicativo empacotado. Linux usa Xvfb para testes gráficos automatizados. macOS usa Tk/Aqua nos testes automatizados; isso não substitui inspeção visual ou teste manual. Windows v1.8.0 ainda não foi testado manualmente em computador de usuário. Linux e macOS também aguardam teste manual em máquinas/distribuições de usuários; compatibilidade ampla entre distribuições Linux não foi certificada.

O aplicativo não exige Python instalado. O pacote macOS tem assinatura ad hoc de empacotamento, sem identidade Developer ID e sem notarização Apple. O Gatekeeper pode exibir um aviso. Consulte [Instalação e uso](INSTALACAO-E-USO.md) para verificar o SHA-256 e as instruções do macOS.

## Visão geral

O operador informa os alvos e credenciais de acesso, analisa os equipamentos, revisa os resultados, salva os exports `.rsc` quando aplicável, confere o plano e aprova explicitamente a manutenção. Uma análise inicial não altera configurações nem reinicia equipamentos.

O NPKManager trabalha com RouterOS v6 ou v7 mantendo a mesma linha principal instalada. Não migra automaticamente de v6 para v7 e não faz downgrade. Equipamentos podem ser bloqueados por verificações de segurança, pacotes, arquitetura, espaço ou outras pré-condições.

Para uma atualização de RouterOS, configure um servidor HTTP ou HTTPS com os NPKs oficiais correspondentes. A operação somente de RouterBOOT não consulta esse servidor. Veja o [guia do servidor NPK](SERVIDOR-NPK.md), que explica a listagem, os nomes aceitos e a seleção de pacotes.

## Modos de operação

| Escopo | Comportamento |
|---|---|
| RouterOS + RouterBOOT | Atualiza RouterOS quando necessário, aguarda e valida o retorno; depois atualiza RouterBOOT se houver firmware novo e valida novamente. Em hardware físico, pode haver até dois reboots. |
| Somente RouterOS | Atualiza RouterOS quando necessário e solicita reboot sem aguardar nem confirmar o retorno nesta execução. Faça novo scan para verificar o resultado. |
| Somente RouterBOOT | Usa o firmware oferecido pelo RouterOS instalado; não baixa NPK. Solicita reboot sem aguardar nem confirmar o retorno nesta execução. |

Em CHR, identificado pelo `board-name=CHR`, RouterBOOT aparece como **N/A (CHR)**. No modo combinado, somente a etapa RouterOS pode ocorrer; não há upgrade nem segundo reboot de RouterBOOT. Se RouterOS já estiver atualizado, não é necessário reboot. As demais verificações continuam ativas. Somente RouterBOOT em CHR é apresentado como não aplicável e não inicia uma operação.

## Segurança e backups

- `flagged=yes` exige revisão manual e bloqueia a atualização automática. O indicador, isoladamente, não prova invasão; o NPKManager nunca o limpa automaticamente.
- `flagged=no` explícito é registrado como `SAFE`, mas não é prova absoluta de ausência de comprometimento.
- Em formatos legados reconhecidos que não expõem o campo, o estado `FLAGGED_FIELD_UNAVAILABLE` informa que não foi possível verificar `flagged`. Não equivale a `SAFE`; a operação só pode prosseguir se as demais verificações forem aprovadas.
- Campo inesperadamente ausente, resposta inválida ou ambígua, erro de consulta/permissão e comando não suportado em firmware moderno permanecem estados distintos e bloqueiam a operação automática.
- O arquivo `.rsc` é um export textual, não um backup completo. Pode conter informações sensíveis; guarde-o e os relatórios em pastas com acesso restrito. O programa não restaura configurações automaticamente.

Consulte [Instalação e uso](INSTALACAO-E-USO.md) para o procedimento completo, interpretação dos resultados, CHR, modos, relatórios e limitações.
