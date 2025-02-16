### 1 - Quais são os principais objetivos de design (projeto) de sistemas distribuídos? Liste e explique brevemente pelo menos três objetivos de design mencionados no livro.

Os sistemas distribuídos são projetados com vários objetivos em mente. Três objetivos principais são:

*   **Compartilhamento de recursos:** Um objetivo importante é facilitar o acesso e o **compartilhamento de recursos remotos** por usuários e aplicações. Esses recursos podem incluir periféricos, armazenamento, dados, arquivos, serviços e redes. O compartilhamento de recursos pode ser motivado por razões econômicas, como ter um único recurso de armazenamento confiável de ponta, em vez de comprar e manter armazenamento para cada usuário separadamente.
*   **Transparência de distribuição:** Os sistemas distribuídos devem ocultar o fato de que os processos e os recursos estão fisicamente distribuídos em vários computadores, possivelmente separados por grandes distâncias. O objetivo é tornar a **distribuição de processos e recursos transparente** para usuários finais e aplicações. Essa transparência é alcançada por meio de middleware.
*   **Escalabilidade:** A escalabilidade é um dos objetivos de design mais importantes para os desenvolvedores de sistemas distribuídos. A escalabilidade refere-se à capacidade do sistema de lidar com um número crescente de usuários ou recursos. Existem três dimensões de escalabilidade: tamanho, geográfica e administrativa. O **tamanho** refere-se à capacidade de lidar com mais usuários ou recursos, o **geográfico** refere-se à capacidade de lidar com componentes geograficamente dispersos e o **administrativo** refere-se à capacidade de abranger vários domínios administrativos.

### 2- Quais são as principais vantagens da utilização de middleware em sistemas distribuídos? Defina middleware e cite ao menos duas vantagens de sua utilização.

*   **Definição:** **Middleware** **é uma camada de software** que fica entre as aplicações e o sistema operacional, oferecendo uma interface comum para diferentes aplicações que podem estar rodando em diferentes máquinas. Ele oculta as complexidades de comunicação e distribuição de recursos, tornando mais fácil para os desenvolvedores criarem aplicações distribuídas.

*   **Vantagens:**
    *   **Transparência de distribuição:** O middleware ajuda a alcançar a **transparência de distribuição**, escondendo a localização dos recursos e como eles são acessados. Ele garante que as aplicações vejam a mesma interface em todos os lugares.
    *   **Interoperabilidade:** O middleware facilita a comunicação entre **aplicações diferentes** que podem ser executadas em sistemas operacionais e hardware diferentes. Ele possibilita que componentes de diferentes fabricantes coexistam e trabalhem juntos. O middleware consegue isso através da padronização das interfaces de comunicação, o que permite que os sistemas interajam sem que os desenvolvedores precisem se preocupar com detalhes de baixo nível, como formatos de dados ou protocolos de rede.

### 3 - Explique os diferentes estilos arquiteturais em sistemas distribuídos. Compare os estilos arquiteturais em camadas, orientados a serviços e publish-subscribe. Qual seria mais adequado para um sistema de monitoramento em tempo real?

Os sistemas distribuídos são organizados em componentes de software que interagem de maneiras específicas. Os principais estilos arquitetônicos são:

*   **Arquiteturas em camadas:** Os componentes são organizados em camadas, onde cada camada usa serviços da camada inferior. Normalmente, uma camada só interage com as camadas adjacentes, o que simplifica a gestão das interações do sistema. As chamadas geralmente ocorrem de cima para baixo, sendo que chamadas de baixo para cima são incomuns. Esse estilo é frequentemente utilizado em protocolos de rede.
*   **Arquiteturas orientadas a serviços (SOA):** O sistema é composto por serviços independentes que podem ser acessados ​​através de interfaces padronizadas. Os serviços são modulares e podem ser facilmente combinados para criar aplicativos mais complexos. Essa arquitetura facilita a composição de diferentes serviços.
*   **Arquiteturas publish-subscribe:** Os componentes (publishers) publicam eventos em canais (topics), e os componentes (subscribers) se inscrevem para receber notificações desses eventos. Os componentes não precisam saber sobre a existência uns dos outros. Isso permite que os processos se comuniquem de forma assíncrona. Os sistemas de publicação-assinatura são particularmente úteis para sistemas de grande escala devido ao forte desacoplamento dos processos.

Para um **sistema de monitoramento em tempo real**, uma arquitetura **publish-subscribe** seria mais adequada, pois permite que os sensores (publishers) enviem dados para os componentes de análise (subscribers) em tempo real e de forma eficiente, sem a necessidade de um relacionamento direto ou dependências entre os componentes. O estilo publish-subscribe também é adequado porque permite adicionar ou remover assinantes sem interromper a funcionalidade do sistema.

### 4 - Analise as limitações da transparência de falhas em sistemas distribuídos. Por que é difícil implementar uma transparência completa de falhas? Cite um exemplo prático onde essa dificuldade se manifesta.

*   A **transparência de falhas** visa ocultar as falhas do sistema dos usuários e aplicações. No entanto, alcançar uma transparência de falhas completa é extremamente difícil, senão impossível, em sistemas distribuídos.

*   **Dificuldades:**
    *   É difícil distinguir entre um processo morto e um processo que está respondendo lentamente.
    *   Mascarar totalmente falhas e sua recuperação é provadamente impossível.
    *   Existe um trade-off entre transparência e desempenho, pois tentar mascarar uma falha de servidor pode atrasar todo o sistema.

*   **Exemplo prático:** Ao entrar em contato com um servidor web ocupado, um navegador pode eventualmente atingir o tempo limite e relatar que a página não está disponível. Nesse ponto, o usuário não consegue determinar se o servidor realmente caiu ou se a rede está congestionada. Essa incerteza destaca a dificuldade de alcançar total transparência de falhas.

### 5 - Descreva a arquitetura de sistemas peer-to-peer. Diferencie sistemas P2P estruturados e não estruturados, citando vantagens e desvantagens de cada abordagem.

Em um sistema **P2P**, todos os nós têm funções iguais e atuam tanto como clientes quanto como servidores. Os nós são organizados em uma rede de sobreposição (overlay). A interação entre os processos é simétrica.
*   **Sistemas P2P estruturados:**
    *   Os nós são organizados em uma topologia específica, como um anel ou um hipercubo.
    *   A topologia é usada para encontrar dados de forma eficiente.
     *  Geralmente usam um índice semântico.
    *   **Vantagens:** Buscas eficientes.
    *   **Desvantagens:** Complexidade na manutenção da topologia.
*   **Sistemas P2P não estruturados:**
    *   Os nós são conectados de forma aleatória.
    *   A busca por dados é feita por meio de inundação (flooding) ou random walks.
    *   **Vantagens:** Flexibilidade para entrada e saída de nós.
    *   **Desvantagens:** Buscas menos eficientes e consumo excessivo de recursos.

### 6 - Como a replicação contribui para a escalabilidade e a disponibilidade em sistemas distribuídos? Explique como a replicação é utilizada para melhorar a escalabilidade e a tolerância a falhas. Quais são os desafios envolvidos na sincronização de réplicas?

*   A **replicação** é a criação de várias cópias de um recurso.
*   **Contribuição para escalabilidade:** A replicação melhora a escalabilidade, distribuindo a carga entre várias cópias. Se um recurso estiver replicado em vários servidores, cada um pode atender a uma parte das solicitações, evitando gargalos e melhorando o desempenho geral do sistema.
*   **Contribuição para disponibilidade (tolerância a falhas):** A replicação aumenta a disponibilidade, garantindo que o serviço continue operando mesmo que algumas cópias falhem . Se um servidor que contém uma réplica ficar inoperante, outras réplicas poderão assumir o controle, minimizando o impacto para o usuário.
*   **Desafios da sincronização de réplicas:**
    *   As réplicas devem ser mantidas consistentes; ou seja, qualquer alteração em uma réplica deve ser propagada para as outras.
    *   A sincronização global das réplicas pode ser difícil de se implementar de forma escalável, especialmente devido às latências de rede. Mecanismos de sincronização precisam ser utilizados para garantir que todas as cópias de um recurso sejam atualizadas corretamente, o que pode ser difícil e complexo.
    *   Pode haver trade-offs entre consistência, replicação e desempenho.