# Histórico de versões

## 1.7.2

- Substituído o bootloader que executava AVX-512 antes da abertura da interface. O pacote v1.7.1 podia encerrar em computadores x64 sem esse recurso.
- A distribuição Windows volta a ser gerada com PyInstaller. As funções de inventário, segurança e atualização permanecem iguais às da v1.7.1.
- Novo executável `NPKManager-1.7.2.exe` com SHA-256 `337D787B54D192D7E360EC5A266ACDD078CBE87D9248C8C283A7DD5BC5B8DD9C`.

## 1.7.1

- Novo ícone oficial multirresolução, redesenhado para melhorar a leitura em tamanhos pequenos.
- No Windows, as janelas preservam o ícone ICO multirresolução.
- Guias públicos e checksum foram atualizados para o executável v1.7.1.

## 1.7.0

- Documentação pública ampliada com instruções de instalação e operação, limites de segurança, backups e preparação do servidor NPK.
- Atualização controlada de RouterOS com análise prévia, revisão do plano e aprovação do operador.
- Suporte aprimorado a MikroTik CHR: RouterBOOT é não aplicável e não provoca um segundo reboot.
- Detalhes do equipamento permitem consultar valores longos com rolagem horizontal.
- Proteções de segurança podem exigir revisão manual ou bloquear a operação quando o estado não puder ser verificado.
- Correções acumuladas melhoram a seleção de pacotes, os prechecks e a apresentação de resultados.
