# NPKManager

**Versão atual: 1.6.1**

O NPKManager ajuda operadores a inventariar equipamentos MikroTik RouterOS, revisar atualizações, salvar exports de configuração e executar atualizações aprovadas.

- [Baixar a versão mais recente em GitHub Releases](https://github.com/guilherme-raber/NPKManager-Releases/releases/latest)
- [Ver todas as versões](https://github.com/guilherme-raber/NPKManager-Releases/releases)
- [Guia de instalação e uso](INSTALACAO-E-USO.md)
- [Histórico de versões](CHANGELOG.md)

> **Disponibilidade:** Ainda não há instaladores publicados neste repositório. Baixe arquivos somente quando forem publicados na página GitHub Releases acima. A página da release informará os sistemas operacionais e arquivos disponíveis.

## Requisitos

- Um computador e sistema operacional compatíveis com os arquivos indicados na release.
- Acesso de rede aos equipamentos e às portas de gerenciamento configuradas. O aplicativo testa Winbox antes de iniciar uma conexão SSH.
- Credenciais autorizadas para os equipamentos que serão administrados.
- Para atualizar RouterOS, um servidor HTTP ou HTTPS acessível com os pacotes NPK oficiais correspondentes à versão e à arquitetura dos equipamentos.
- Espaço local para guardar os exports de configuração e relatórios.

O executável independente para Windows, quando disponibilizado, não exige instalação separada do Python. Não presuma suporte a um sistema operacional que não esteja indicado nos arquivos da respectiva release.

## Fluxo básico

1. Configure os alvos, as portas, as credenciais e o servidor de pacotes.
2. Analise a rede e revise os estados apresentados.
3. Selecione os equipamentos elegíveis e salve os exports de configuração em uma pasta protegida.
4. Confira o plano de atualização e aprove explicitamente a operação.
5. Guarde os relatórios e faça uma nova análise para confirmar o resultado quando necessário.

A análise inicial não atualiza nem reinicia equipamentos. O NPKManager não faz migração automática entre RouterOS 6 e 7 nem downgrade.

## Segurança

flagged=yes é um indicador que exige revisão manual; isoladamente, não prova invasão. O NPKManager bloqueia a atualização automática nesse estado e nunca limpa o indicador. Em firmware legado que não expõe o campo, o estado pode aparecer como FLAGGED_FIELD_UNAVAILABLE: isso significa que não foi possível verificar flagged e não equivale a SAFE. Respostas inválidas, ambíguas ou erros de consulta também podem bloquear o equipamento.

Exports .rsc podem conter informações sensíveis. Guarde-os com acesso restrito e não os envie publicamente.

Leia o [guia de instalação e uso](INSTALACAO-E-USO.md) antes de executar uma manutenção.
