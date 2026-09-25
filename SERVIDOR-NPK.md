# Preparar o servidor de pacotes NPK

O NPKManager usa os arquivos `.npk` hospedados pelo operador. Ele não consulta automaticamente o catálogo online da MikroTik. A pasta publicada precisa oferecer uma listagem HTML de arquivos com links clicáveis e acesso HTTP ou HTTPS aos próprios NPKs.

## URL e estrutura da pasta

Informe no NPKManager a URL da pasta, sem usuário/senha embutidos, parâmetros ou fragmentos. Por exemplo:

```text
https://downloads.example.net/routeros/
```

Se for informado somente o host ou a raiz (`https://downloads.example.net/`), o NPKManager usa a pasta `/routeros/`. A listagem precisa estar habilitada para essa pasta e mostrar links para os arquivos. Não basta que os NPKs sejam acessíveis por URLs conhecidas se a listagem não for consultável.

Coloque os arquivos necessários diretamente na pasta publicada. O NPKManager lê os links da listagem; não percorre subpastas.

```text
routeros/
├── routeros-7.24.2-arm64.npk
├── routeros-7.24.2-arm.npk
├── routeros-7.24.2-x86_64.npk
├── routeros-7.24.2.npk
├── wireless-7.24.2-arm.npk
└── wifi-qcom-7.24.2-arm64.npk
```

Os nomes acima são exemplos reconhecidos, não uma lista universal de requisitos. Os arquivos efetivamente necessários dependem da arquitetura, da linha RouterOS e dos pacotes instalados em cada equipamento. Não substitua um NPK por outro conteúdo mantendo o mesmo nome durante uma manutenção.

## Como o catálogo é lido e selecionado

Em um scan que inclui RouterOS, o NPKManager consulta a listagem HTML da pasta configurada, identifica os links que correspondem ao padrão de nome NPK reconhecido e consulta o tamanho dos arquivos. A versão-alvo é a maior versão numérica estável disponível que corresponda à arquitetura e à linha principal instalada:

- RouterOS v6 procura pacotes v6 compatíveis; RouterOS v7 procura pacotes v7 compatíveis.
- O programa não faz downgrade nem migração automática v6 → v7.
- A data de modificação do arquivo não decide qual versão é escolhida.
- Nomes beta/RC não são alvos numéricos aceitos pelo catálogo.
- A operação somente de RouterBOOT não consulta o catálogo.

O servidor deve retornar tamanho válido por `Content-Length` (normalmente em uma requisição `HEAD`) ou permitir uma consulta `Range` que informe o tamanho total em `Content-Range`. Se o servidor não informar um tamanho válido, o arquivo não entra no plano.

## Nomes reconhecidos

Os exemplos abaixo correspondem aos formatos aceitos na versão 1.7.1. Mantenha os nomes originais dos pacotes oficiais:

```text
# RouterOS v7 — arquitetura depois da versão
routeros-7.24.2-arm64.npk
routeros-7.24.2-arm.npk
routeros-7.24.2-ppc.npk
routeros-7.24.2-x86_64.npk          # arquitetura x86_64, usada por CHR
routeros-7.24.2.npk                 # interpretado como arquitetura x86

# RouterOS v6 — arquitetura antes da versão
routeros-arm64-6.49.21.npk
routeros-powerpc-6.49.21.npk
routeros-x86-6.49.21.npk

# Pacotes adicionais
wireless-7.24.2-arm.npk
wifi-qcom-7.24.2-arm64.npk
```

O nome sem arquitetura explícita para o pacote principal `routeros` é interpretado como x86; ele não é tratado como `x86_64`. Para CHR cujo inventário informa `architecture-name=x86_64`, use o nome com `-x86_64`. A grafia `powerpc` dos nomes v6 é normalizada para a arquitetura `ppc` na comparação. Confirme os nomes e a arquitetura com o pacote oficial destinado aos seus equipamentos.

## Pacotes adicionais

O NPKManager considera os pacotes instalados ao montar o plano. Quando um pacote adicional instalado for necessário na versão-alvo, o pacote correspondente precisa estar na pasta com a mesma versão e arquitetura compatível. Se estiver ausente, a operação poderá ser bloqueada para evitar atualizar somente parte do conjunto necessário.

Um pacote que aparece no RouterOS como disponível, mas não instalado, não é automaticamente tratado como instalado. A linha v6 pode exigir o bundle oficial completo; instalações v6 compostas somente por pacotes individuais precisam de preparação manual. A transição entre famílias de wireless, como `wifiwave2` e `wifi-qcom`, também pode exigir planejamento manual. O NPKManager não converte automaticamente a configuração nem resolve essas dependências.

## HTTP, HTTPS e caminho de transferência

HTTPS é recomendado. O computador que executa o NPKManager precisa alcançar a listagem e os arquivos. Se `fetch` estiver habilitado, o roteador também precisa alcançar o servidor para baixar os NPKs por `/tool fetch`; para HTTPS, o comando solicita validação do certificado. Se `fetch=no`, o computador baixa o arquivo e o envia ao roteador por SFTP, usando a sessão SSH.

HTTP explícito também é aceito, mas não oferece confidencialidade nem proteção TLS contra alteração no caminho. Use servidor confiável e prefira HTTPS.

Há uma exceção de compatibilidade: ao consultar o servidor pelo computador, se uma URL HTTPS sem porta explícita falhar por erro TLS, conexão ou timeout, o NPKManager pode tentar uma vez o mesmo endereço por HTTP e registra o fallback. Uma URL HTTPS com porta explícita não sofre esse downgrade automático. Esse fallback não significa que um certificado inválido foi aceito; significa que a conexão passa a HTTP sem TLS. Revise o aviso apresentado e não trate HTTP como equivalente a HTTPS. Uma falha no `/tool fetch` executado pelo roteador não usa esse fallback do cliente HTTP.

O NPKManager compara o tamanho baixado com o tamanho informado pelo servidor. Quando o envio é por SFTP, também valida a cópia com SHA-256. A aplicação não compara o NPK a um hash remoto publicado no catálogo; use os pacotes oficiais e proteja o servidor contra substituição indevida. A assinatura do pacote é verificada pelo próprio RouterOS durante a instalação.

## Checklist do operador

- A URL aponta para a pasta correta e usa uma porta acessível.
- A listagem HTML exibe diretamente os NPKs, sem exigir login interativo do navegador.
- O servidor entrega os arquivos e anuncia tamanho via `Content-Length` ou `Content-Range`.
- Os nomes são originais e correspondem à versão, arquitetura e pacotes extras necessários.
- Os NPKs estão na pasta consultada, não em subpastas.
- O roteador pode alcançar o servidor quando será usado `/tool fetch`; caso contrário, verifique que o caminho por SFTP está disponível e autorizado.
- A pasta não será modificada durante o lote de manutenção.
