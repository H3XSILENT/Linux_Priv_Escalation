# Kernels 3xploits
O escalonamento de privilégios idealmente leva a privilégios de root. Isso pode às vezes pode ser alcançado simplesmente explorando uma vulnerabilidade existente, ou em alguns casos acessando outra conta de usuário que tenha mais privilégios, informações ou acesso.

A menos que um único vulnerabilidade leva a um shell root, o processo de escalonamento de privilégios dependerá de configurações incorretas e permissões frouxas.

O kernel em Linux os sistemas gerenciam a comunicação entre componentes como o memória no sistema e aplicativos. Esta função crítica requer que o kernel tenha privilégios específicos; portanto, um sucesso exploit potencialmente levará a privilégios de root.

---
A exploração do Kernel a metodologia é simples:

1. Identifique o versão do kernel

2. Pesquise e encontre um código de exploração para a versão do kernel do sistema de destino

3. Execute o exploit

Embora pareça simples, lembre-se de que uma falha na exploração do kernel pode levar a uma falha do sistema. Certifique-se de que este resultado potencial seja aceitável dentro o escopo do seu trabalho de teste de penetração antes de tentar um exploração do kernel.

Fontes de pesquisa:

* Com base em suas descobertas, você pode usar o Google para pesquisar um código de exploração existente.

* Fontes como https://www.cvedetails.com/ também pode ser útil.

* Outra alternativa seria usar um script como LES (Linux Explore Suggester), mas lembre-se de que essas ferramentas podem gerar falsos positivos (relatar uma vulnerabilidade do kernel que não afeta o sistema de destino) ou falsos negativos (não relatar nenhuma vulnerabilidade do kernel, embora o kernel seja vulnerável).

>Certifique-se de entender como o código de exploração funciona ANTES de iniciá-lo. Alguns códigos de exploração podem fazer alterações no sistema operacional que os tornariam inseguros em uso posterior ou fazer alterações irreversíveis no sistema, criando problemas posteriormente. É claro que essas podem não ser grandes preocupações em um ambiente de laboratório ou CTF, mas são totalmente proibidas durante um teste de penetração real.
