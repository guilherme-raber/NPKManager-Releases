# Instalação e uso — NPKManager 1.7.0

Este guia descreve a distribuição pública do NPKManager 1.7.0 para Windows x64. A aplicação realiza operações administrativas em roteadores; use-a apenas em equipamentos sob sua responsabilidade e dentro de uma janela de manutenção aprovada.

## Baixar e iniciar

1. Abra a página de [Releases](https://github.com/guilherme-raber/NPKManager-Releases/releases/latest).
2. Em **Assets**, baixe `NPKManager-1.7.0.exe` e salve-o em uma pasta local.
3. Execute o arquivo no Windows x64. Python não precisa estar instalado.

O executável foi testado no ambiente Windows disponível, mas não em uma instalação Windows limpa. Não há uma versão mínima oficial do Windows declarada. Se o Windows ou as políticas da sua organização exibirem um aviso, confirme a origem do arquivo e siga as políticas de segurança locais antes de executá-lo.

SHA-256 publicado para `NPKManager-1.7.0.exe`:

```text
94c3a0708bc763448160218726766e76ca06d1aaaef3ed61da85035e7c59d041
```

## Preparação

Antes de analisar uma rede:

- Tenha autorização para acessar e manter os equipamentos e planeje o impacto dos reinícios.
- Confirme a conectividade do computador com a porta Winbox e a porta SSH configuradas. O scan testa Winbox primeiro; somente se essa porta responder tenta SSH. A identificação do RouterOS e a administração são feitas por SSH.
- Use uma conta RouterOS autorizada a consultar as informações e executar as ações escolhidas. O programa não altera serviços para contornar falta de acesso.
- Escolha uma pasta local protegida para os exports `.rsc` e outra para os relatórios. Um export pode conter segredos de configuração.
- Se RouterOS fizer parte do escopo, prepare o servidor conforme [Servidor NPK](SERVIDOR-NPK.md). Somente RouterBOOT não usa catálogo NPK.

As portas Winbox e SSH são informadas pelo operador e precisam ser diferentes. A senha SSH é usada durante a sessão e não é persistida no perfil de configurações.

## Primeira configuração

Na tela inicial, informe:

1. **Alvos:** um endereço IPv4, uma rede IPv4 ou uma lista importada. Exemplos de formato de rede: `192.0.2.0/24` ou `192.0.2.0:24`.
2. **Portas Winbox e SSH**, usuário e senha autorizados.
3. **Servidor NPK** em HTTP ou HTTPS quando o escopo incluir RouterOS.
4. **Pasta de relatórios** local.
5. **Escopo de manutenção**: RouterOS + RouterBOOT, Somente RouterOS ou Somente RouterBOOT.

TXT, CSV e LOG podem ser importados. O importador procura endereços IPv4 válidos no texto, preserva a ordem da primeira ocorrência e remove duplicados. A importação substitui a lista anterior. O limite é 65.536 endereços por scan. Alterar manualmente o campo de alvos ou usar **Limpar** remove a lista importada da sessão.

É possível salvar parâmetros de conexão não secretos, como portas, usuário, protocolo, servidor NPK e pasta de relatórios. A senha, os alvos, a seleção e o estado da manutenção não são salvos no perfil.

## Fluxo de análise e aprovação

1. Clique em **Analisar rede**. A análise consulta os equipamentos e prepara um plano, sem alterar configurações nem reiniciar.
2. Revise o estado, as versões, a arquitetura, os pacotes, o espaço disponível, as verificações de segurança e as mensagens de bloqueio. **Incluir bloqueados e atualizados** permite consultar também esses resultados.
3. Selecione os equipamentos que pretende manter. **Selecionar elegíveis** considera também o estado de segurança; ainda assim, confira cada linha e cada motivo antes de continuar.
4. Avance para **Salvar backup**. Escolha a pasta e salve os exports `.rsc`. O equipamento é revalidado durante esta etapa.
5. Revise novamente os equipamentos e o plano. O backup deve estar pronto e íntegro antes de aprovar a operação.
6. Clique em **Aprovar e iniciar atualização** e confirme o resumo. A confirmação é a autorização explícita para executar as ações mostradas.
7. Acompanhe o resultado na interface, no Debug CLI e nos arquivos de relatório. Em modos sem espera pelo retorno, execute um novo scan para confirmar a versão real.

O modo combinado espera o retorno do RouterOS para validar a instalação antes de avançar para RouterBOOT. Se RouterOS e RouterBOOT precisarem de atualização em hardware físico, podem ocorrer até dois reinícios. Se somente uma etapa precisar de atualização, haverá somente o reinício daquela etapa. O CHR não tem RouterBOOT aplicável.

## Escopos RouterOS e RouterBOOT

### RouterOS + RouterBOOT

O plano pode instalar RouterOS e, depois da revalidação, RouterBOOT. A versão principal é preservada: RouterOS v6 permanece na linha v6 e v7 permanece na v7. O NPKManager não faz downgrade nem migração automática v6 → v7. Se o equipamento já estiver na versão RouterOS pretendida, somente RouterBOOT poderá permanecer pendente.

Em CHR, apenas RouterOS pode ser atualizado; RouterBOOT é **N/A (CHR)**. Se RouterOS já estiver atualizado, CHR pode aparecer como **Atualizado**, sem ficar pendente por RouterBOOT.

### Somente RouterOS

Atualiza RouterOS quando houver um alvo compatível. Após transferir os pacotes, solicita reboot e encerra essa operação sem aguardar nem confirmar o retorno. RouterBOOT não é atualizado neste escopo. Faça outro scan para conferir o equipamento após o reinício.

### Somente RouterBOOT

Usa o firmware disponibilizado pelo RouterOS instalado. Não consulta o servidor NPK nem baixa pacotes RouterOS. Se o firmware precisar de upgrade, o programa solicita o upgrade e o reboot, sem aguardar nem confirmar o retorno nessa execução; faça novo scan para verificar.

Em CHR, RouterBOOT não se aplica: o resultado informa **Não aplicável — RouterBOOT: N/A (CHR)** e nenhuma operação ou reboot é iniciado por RouterBOOT. Em equipamento que não seja CHR e cuja condição de RouterBOARD não pôde ser confirmada, a operação é bloqueada.

## CHR

O NPKManager identifica CHR pelo valor de inventário `board-name=CHR`. A arquitetura `x86` ou `x86_64`, sozinha, não é suficiente para classificar um equipamento como CHR.

RouterBOOT aparece separadamente como **N/A (CHR)**, distinto de firmware desconhecido ou falha de consulta. No escopo combinado, CHR segue normalmente as verificações e a atualização de RouterOS, mas não executa `/system routerboard upgrade`, não espera um segundo reboot e não valida firmware RouterBOOT. As verificações de segurança, pacotes, arquitetura, espaço e demais prechecks continuam valendo.

## Servidor de pacotes

O computador precisa consultar a listagem HTML da pasta NPK e seus tamanhos. Quando `fetch` está habilitado, o roteador também precisa alcançar os arquivos para baixá-los diretamente. Quando `fetch=no`, o computador baixa o arquivo e o envia ao equipamento por SFTP usando a sessão SSH. Veja [Preparação do servidor NPK](SERVIDOR-NPK.md) para requisitos, nomes e exemplos.

Use apenas pacotes oficiais MikroTik, completos, com nomes originais. O programa não consulta o catálogo online da MikroTik. Pacotes extras instalados que sejam necessários à configuração precisam estar disponíveis para a mesma versão e arquitetura; se um pacote necessário faltar no servidor, o equipamento poderá ser bloqueado em vez de prosseguir com um conjunto incompleto.

## Segurança e interpretação dos estados

- **`flagged=no` → `SAFE`:** o equipamento apresentou explicitamente o valor `no`. Isso não é prova absoluta de que nunca houve comprometimento.
- **`flagged=yes` → `SECURITY_REVIEW_REQUIRED`:** a atualização automática é bloqueada e o equipamento exige revisão manual. O indicador é um alerta de segurança, não uma prova isolada de invasão. Não tente contornar o bloqueio durante a manutenção.
- **`FLAGGED_FIELD_UNAVAILABLE`:** certos formatos legados reconhecidos não apresentam o campo. O estado significa “não verificado”; não equivale a `SAFE`. As demais pré-condições e a aprovação explícita continuam necessárias.
- **`UNKNOWN`, `ERROR`, `INVALID` e `NOT_SUPPORTED`:** campo inesperadamente ausente, falha de leitura/permissão, resposta inválida ou ambígua e comando não suportado em situação moderna. Esses estados bloqueiam a operação automática.

O NPKManager nunca executa `/system/device-mode/update flagged=no` nem limpa o indicador automaticamente. Uma revisão manual deve ocorrer fora do fluxo automático de atualização.

Outras verificações também podem bloquear um equipamento, por exemplo versão em beta/RC, incompatibilidade de arquitetura ou pacote, pouco espaço, pacote `.npk` já presente no roteador, falta de permissões ou estado de inventário inconsistente. Corrija ou investigue o motivo e faça novo scan; não presuma que a seleção do equipamento removeu o bloqueio.

## Backups e relatórios

O arquivo `.rsc` é um export textual obtido por SSH: RouterOS v7 usa `/export show-sensitive` e RouterOS v6 usa `/export`. Ele não é um backup binário completo e não substitui os procedimentos próprios de recuperação. Entre os itens não cobertos pelo export estão senhas de usuários do sistema, certificados, chaves SSH e bases de dados como Dude/User Manager. O conteúdo exportado pode incluir outros dados sensíveis; restrinja o acesso aos arquivos.

O NPKManager não cria nem restaura automaticamente um backup binário, não faz rollback e não importa o RSC após uma falha. O RSC local é conferido antes da atualização e preservado após sucesso ou falha. O conteúdo do export não é escrito no log.

Na pasta de relatórios, a sessão gera um `.log` com o andamento, etapas e mensagens de diagnóstico, e um `.json` estruturado com informações dos equipamentos, versões, estados, evidências, caminho do backup e resultado, atualizado ao concluir cada etapa. Esses arquivos podem revelar IPs, identificadores e detalhes operacionais; guarde-os em local restrito e compartilhe somente após revisar seu conteúdo. A senha é ocultada nos registros da aplicação.

Se o equipamento não retornar após reiniciar, o relatório ajuda a identificar a última etapa, mas não inicia recuperação automática. Dependendo do modelo e do estado, a recuperação pode exigir acesso local e ferramentas oficiais da MikroTik. Um novo scan verifica o estado atual; o NPKManager não retoma automaticamente uma sessão interrompida.

## Concorrência e fila

O scan usa até 32 conexões simultâneas por padrão (configurável de 1 a 64). O limite de atualizações simultâneas é separado e começa em 1 (configurável de 1 a 64). A espera de reboot começa em 300 segundos e pode ser configurada entre 60 e 3.600 segundos.

Use concorrência somente entre equipamentos cuja manutenção possa ocorrer em paralelo. O programa não descobre dependências de topologia. **Parar fila** impede o início de novos equipamentos, mas deixa operações já iniciadas terminarem. Uma falha pausa a fila para revisão.

## Limitações importantes

- Não migra automaticamente RouterOS v6 para v7 e não faz downgrade.
- Não identifica dependências de topologia nem coordena serviços entre roteadores.
- Um resultado atualizado confirma as versões e verificações implementadas, mas não comprova funcionamento de PPPoE, VPN, Wi-Fi, roteamento, enlaces ou tráfego dos clientes. Valide esses serviços após a manutenção.
- Pacotes extras incompatíveis ou ausentes podem bloquear a operação; dependências de wireless podem exigir planejamento manual.
- O export `.rsc` não é um backup completo do equipamento e não há restauração automática.
- Nos modos Somente RouterOS e Somente RouterBOOT, o retorno pós-reboot não é validado na mesma operação; é necessário novo scan.
- O download verifica tamanho esperado; quando o envio é feito por SFTP, a integridade da cópia é conferida por SHA-256. O NPKManager não autentica o arquivo por um hash publicado no catálogo. Use HTTPS e fontes oficiais confiáveis.
- Windows limpo não foi validado; não é declarada uma versão mínima oficial nem compatibilidade universal.

## Solução de problemas

- **Nenhum equipamento aparece:** confirme IPs, máscara, conectividade e porta Winbox. Uma porta Winbox fechada faz o scan ignorar aquele endereço sem tentar SSH.
- **Falha de conexão ou autenticação:** confira porta SSH, usuário, senha, permissões e alcance de rede. A senha precisa ser informada novamente a cada sessão.
- **Nenhum pacote encontrado:** confirme URL, acesso à listagem de arquivos, nomes NPK, arquitetura e linha v6/v7. Consulte [Servidor NPK](SERVIDOR-NPK.md).
- **Pacote extra ausente:** publique na pasta correta a versão oficial correspondente, com nome original, e faça novo scan.
- **Falta de espaço:** revise armazenamento e pacotes planejados no equipamento; não remova arquivos automaticamente pelo NPKManager.
- **`flagged` ou device-mode bloqueado:** mantenha o bloqueio e faça revisão manual do equipamento. Não tente limpar o indicador pelo NPKManager.
- **Modo separado mostra reboot não verificado:** aguarde o equipamento retornar e faça novo scan.
- **Equipamento não retorna:** consulte as mensagens `FALHA EM` e `ATENÇÃO FINAL` no `.log` e os campos de etapa/estado do `.json`; siga o procedimento de recuperação apropriado ao modelo.
