# Guia Prático de Implementação de Jogos Phaser em Linux Mint: Da Configuração do Ambiente à Execução Final na ThinkPad T420

Este relatório apresenta um tutorial detalhado e prático para a configuração de um ambiente de desenvolvimento de jogos utilizando a stack tecnológica Node.js e o framework Phaser. O guia foi meticulosamente elaborado para atender às especificações técnicas da máquina-alvo: um laptop ThinkPad T420 rodando Linux Mint 22.3 Zena (base Ubuntu 24.04), com processador Intel Core i7-2760QM e uma GPU integrada Intel HD Graphics 3000. A abordagem deste documento foca exclusivamente nos aspectos técnicos de implementação, desde a preparação inicial do sistema até a execução e construção de um projeto de jogo funcional, fornecendo soluções proativas para os desafios inerentes ao hardware especificado.

## Preparação do Ambiente de Desenvolvimento e Instalação do Node.js

A fundação de qualquer projeto de desenvolvimento de software moderno reside na correta configuração do ambiente de trabalho. Para projetos baseados em Node.js e frameworks como Phaser, esta etapa envolve não apenas a instalação de dependências do sistema operacional, mas também a seleção e gestão cuidadosa da versão de Node.js. Esta secção detalha o processo completo, desde a atualização do sistema Linux Mint até à implementação robusta de uma versão LTS do Node.js através do Node Version Manager (NVM), garantindo um ponto de partida sólido e flexível para o desenvolvimento do jogo.

O primeiro passo fundamental é garantir que o sistema operacional esteja completamente atualizado. Isso não apenas melhora a segurança geral do sistema, mas também assegura a compatibilidade das bibliotecas de baixo nível necessárias para compilar alguns pacotes nativos do Node.js. Para o Linux Mint 22.3 Zena, baseado no Ubuntu 24.04, a utilização do gerenciador de pacotes `apt` é o método padrão [[140](https://forums.linuxmint.com/viewtopic.php?t=465686)]. É crucial executar os seguintes comandos num terminal para sincronizar a lista de pacotes com os repositórios remotos e aplicar todas as atualizações disponíveis:

```bash
sudo apt update && sudo apt upgrade -y
```

Além das atualizações do sistema, algumas ferramentas de linha de comando serão indispensáveis durante todo o processo de desenvolvimento. O `git` é essencial para controle de versão, enquanto o `curl` é frequentemente utilizado para baixar scripts de instalação, como o do NVM. Estes pré-requisitos podem ser instalados com um único comando:

```bash
sudo apt install -y git curl
```

Uma vez que o sistema está preparado, a atenção deve voltar-se para o coração do ecossistema JavaScript: o Node.js e o seu gerenciador de pacotes, o npm. A versão padrão de Node.js disponível nos repositórios padrão do Linux Mint 22.x é a 18.19.x [[44](https://linuxcapable.com/how-to-install-node-js-on-linux-mint/)]. Embora funcional para muitos projetos, esta versão pertence à Linha de Suporte Ativo (LTS) antiga e já chegou ao fim do seu ciclo de vida (End-of-Life - EOL) [[44](https://linuxcapable.com/how-to-install-node-js-on-linux-mint/)]. Projetos novos, especialmente aqueles que utilizam construtores modernos como o Vite, beneficiam significativamente de recursos mais recentes do JavaScript (ES6+), otimizações de desempenho e melhorias de segurança presentes nas versões mais recentes do Node.js, como a 20.x ou 22.x [[42](https://www.reddit.com/r/node/comments/1i68oyz/installing_on_linux_mint_22_so_confused/), [91](https://serverfault.com/questions/1117810/install-with-apt-an-old-version-of-node-on-ubuntu-22-04)].

Existem vários métodos para instalar uma versão mais recente do Node.js, cada um com suas próprias considerações. A forma mais direta é utilizar o gerenciador de pacotes do sistema (`sudo apt install nodejs`) [[41](https://forums.linuxmint.com/viewtopic.php?t=358731), [138](https://wiki.crowncloud.net/?How_to_Install_Latest_Node_js_on_Ubuntu_24_04_with_PPA)], mas isto frequentemente resulta na versão mais antiga disponível para a distribuição [[32](https://www.progressiverobot.com/2026/02/05/install-node-js-on-ubuntu/)]. Uma segunda opção é utilizar o repositório oficial da NodeSource, que oferece pacotes predefinidos para várias versões do Node.js [[46](https://www.cherryservers.com/blog/install-npm-ubuntu), [47](https://www.serverpoint.com/en/how-tos/install-nodejs-ubuntu-24/)]. Este método é confiável e mantém o Node.js como um pacote de sistema gerenciado pelo `apt`. O processo envolve adicionar a chave GPG do repositório e o próprio repositório à lista de fontes do sistema, seguido pela instalação via `apt`. No entanto, para um desenvolvedor que possa trabalhar em múltiplos projetos com diferentes requisitos de versão de Node.js, o método mais flexível e altamente recomendado é o uso do Node Version Manager (NVM).

O NVM é uma ferramenta de linha de comando que permite gerenciar múltiplas versões simultâneas do Node.js. Ele instala as versões do Node.js dentro do diretório pessoal do usuário (`.nvm`), evitando conflitos com a versão global do sistema. Esta abordagem é ideal porque permite que cada projeto use explicitamente a versão de Node.js para a qual foi projetado, sem afetar outras aplicações ou o sistema operacional [[44](https://linuxcapable.com/how-to-install-node-js-on-linux-mint/), [45](https://ostechnix.com/install-node-js-linux/)]. A instalação do NVM é simples e envolve a execução de um script de instalação:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.6/install.sh | bash
```

Após a execução do script, é necessário reiniciar o shell ou recarregar o perfil de login para que as funções do NVM sejam carregadas no ambiente atual. Isto pode ser feito executando `. "$HOME/.nvm/nvm.sh"` ou simplesmente abrindo um novo terminal [[36](https://nodejs.org/en/download)]. Com o NVM instalado, é possível listar todas as versões disponíveis para instalação e instalar uma versão específica. Para este projeto, a instalação da última versão LTS é a escolha mais prudente, pois garante estabilidade e suporte contínuo. Isso pode ser feito com o seguinte comando:

```bash
nvm install --lts
```

Este comando instalará a versão LTS mais recente disponível (por exemplo, v24.x.x no momento da escrita) e a tornará a versão padrão para o terminal atual. Para confirmar que a instalação foi bem-sucedida e que a versão correta está em uso, execute os seguintes comandos:

```bash
node -v  # Deve exibir a versão LTS, por exemplo, v24.x.x
npm -v   # Deve exibir a versão do npm correspondente, por exemplo, 11.x.x
```

Para maior praticidade e reprodutibilidade, é uma boa prática criar um ficheiro `.nvmrc` no diretório raiz do projeto. 
```
node -v > .nvmrc
```

Este ficheiro simples contém o número da versão do Node.js que o projeto requer (por exemplo, `24`). Ao navegar para o diretório do projeto, o comando `nvm use` irá automaticamente detectar este ficheiro e alternar para a versão de Node.js especificada, garantindo que todos os desenvolvedores e ambientes de CI/CD utilizem a mesma versão [[44](https://linuxcapable.com/how-to-install-node-js-on-linux-mint/)]. A seguir, uma tabela resume os métodos de instalação de Node.js discutidos:

| Método de Instalação | Comando Principal | Gerenciamento de Versões | Impacto no Sistema | Recomendação |
| :--- | :--- | :--- | :--- | :--- |
| Gerenciador de Pacotes APT | `sudo apt install nodejs` [[41](https://forums.linuxmint.com/viewtopic.php?t=358731)] | Limitado; geralmente a versão mais antiga disponível [[32](https://www.progressiverobot.com/2026/02/05/install-node-js-on-ubuntu/)]. | Modifica o sistema globalmente. | Não recomendado para novos projetos. |
| NodeSource Repository | `curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash - && sudo apt install -y nodejs` [[46](https://www.cherryservers.com/blog/install-npm-ubuntu)] | Permite instalar uma versão específica do repositório [[47](https://www.serverpoint.com/en/how-tos/install-nodejs-ubuntu-24/)]. | Instala como pacote de sistema gerenciado pelo `apt`. | Bom para servidores onde uma única versão global é necessária. |
| Node Version Manager (NVM) | `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.6/install.sh \| bash` [[36](https://nodejs.org/en/download)] | Gerencia múltiplas versões per-project [[44](https://linuxcapable.com/how-to-install-node-js-on-linux-mint/)]. | Instala dentro do diretório do usuário, sem alterar o sistema. | Altamente recomendado para desenvolvimento local. |

Ao seguir estas etapas, o ambiente de desenvolvimento estará tecnicamente preparado para receber o framework Phaser e suas dependências. A escolha do NVM não é apenas uma conveniência, mas uma decisão estratégica que promove a portabilidade, a consistência e a manutenibilidade do projeto a longo prazo, protegendo-o contra problemas de compatibilidade de versão que poderiam surgir com o tempo. A preparação do sistema e a instalação cuidadosa do Node.js constituem a base sobre a qual toda a arquitetura de desenvolvimento será construída.

## Criação da Estrutura do Projeto Phaser com Vite

Com o ambiente Node.js corretamente configurado, o próximo passo decisivo é a criação da estrutura inicial do projeto Phaser. Esta fase vai além da mera criação de pastas e ficheiros; envolve a escolha de uma ferramenta de empacotamento moderna e a inicialização do framework de uma maneira que otimize o fluxo de trabalho de desenvolvimento. A escolha de Vite como o bundler para este projeto é central para a eficiência e velocidade do ciclo de desenvolvimento, especialmente quando comparado com as alternativas mais tradicionais como o Webpack. Esta secção detalha o processo de inicialização de um projeto Phaser com Vite, explicando a importância desta escolha e descrevendo a estrutura de ficheiros resultante.

Vite, criado por Evan You (o criador do Vue.js), é um construtor de front-end emergente que tem rapidamente ganhado popularidade devido à sua velocidade excepcional, principalmente no modo de desenvolvimento [[26](https://www.reddit.com/r/phaser/comments/ojcigr/vite_is_better_for_phaser_than_webpack_and_rollup/), [72](https://flaviocopes.com/phaser-setup/)]. Em contraste com o Webpack, que analisa e agrupa todo o projeto em um único ou poucos pontos de entrada antes de iniciar o servidor, o Vite aproveita as capacidades nativas do navegador para servir os módulos JavaScript em formato ESM (ECMAScript Modules) diretamente do disco [[118](https://www.youtube.com/watch?v=ByG1WnUZDog)]. Isto elimina a necessidade de uma "compilação" demorada do projeto inteiro, resultando numa inicialização quase instantânea do servidor de desenvolvimento, mesmo em projetos grandes [[125](https://frontscope.dev/blog/vite-vs-webpack-vs-rspack-2026/), [157](https://dev.to/abhinav_sharma_e01f930be6/vite-vs-webpack-which-one-is-right-for-your-project-886)]. Além disso, o Vite integra nativamente o Hot Module Replacement (HMR), que permite que as alterações no código se reflitam no navegador quase instantaneamente, sem recarregar a página, proporcionando uma experiência de desenvolvimento extremamente fluida [[127](https://2024.stateofjs.com/en-US/libraries/build_tools/), [131](https://dev.to/themachinepulse/why-i-chose-vite-over-webpack-10x-faster-builds-instant-hmr-8fp)].

As vantagens do Vite em relação ao Webpack são notáveis em benchmarks de desempenho. Testes comparativos mostram que o servidor de desenvolvimento do Vite pode ser até 40 vezes mais rápido que o do Webpack, o HMR pode ser 68 vezes mais rápido, e as compilações de produção podem ser 21 vezes mais rápidas [[131](https://dev.to/themachinepulse/why-i-chose-vite-over-webpack-10x-faster-builds-instant-hmr-8fp)]. Para um desenvolvedor de jogos, onde o ciclo de iteração — escrever código, executar e observar o resultado — é fundamental, essa economia de tempo representa uma vantagem competitiva significativa. Por estas razões, a comunidade Phaser e os templates oficiais têm-se concentrado cada vez mais no suporte ao Vite [[25](https://phaser.io/news/2024/01/phaser-vite-typescript-template), [70](https://phaser.io/news/2024/01/phaser-vite-template), [71](https://github.com/phaserjs/template-vite)]. Dado que o objetivo deste guia é fornecer o caminho mais eficiente para a execução do projeto, o Vite é a escolha óbvia.

### 1. Criação do Projeto Phaser + Vite

No seu terminal, dentro do diretório onde deseja criar o projeto, execute:

```bash
npm create @phaserjs/game@latest guardioes-da-escola
```

Quando o assistente interativo iniciar, selecione as seguintes opções para garantir compatibilidade com o GDD e performance na sua GPU Intel HD 3000:

1.  **Select a template:** Escolha **Web Bundler** (não selecione frameworks como React/Vue, pois o GDD especifica Phaser puro com JS ES6+).
2.  **Select a bundler:** Escolha **Vite** (conforme seção 19.2 do documento de visão).
3.  **Language:** Escolha **JavaScript** (o GDD não exige TypeScript e JS reduz a complexidade inicial para o prazo de 6 semanas).
4.  **Demo Game:** Selecione **None / Empty Template** (você construirá a estrutura do zero conforme a seção 19.4 do GDD; demos adicionam código desnecessário).

> ⚠️ **Nota Importante sobre Versões:** O README do template base menciona Phaser 4.0.0, mas o Documento de Visão do projeto (seção 19.1) especifica **Phaser 3**. Após a criação, verifique o `package.json`. Se vier com Phaser 4, faça o downgrade para manter conformidade com o planejamento acadêmico:
> ```bash
> cd guardioes-da-escola
> npm install phaser@3
> ```

### 2. Instalação de Dependências e Ferramentas Complementares

Com o projeto criado, instale as dependências base e as ferramentas recomendadas na seção 19.2 do GDD:

```bash
# Instala dependências do template
npm install

# Instala Electron para empacotamento desktop (RNF-03 e RNF-14)
npm install --save-dev electron electron-builder

# Instala Vitest para testes unitários de regras de domínio (Seção 25.1)
npm install --save-dev vitest
```

### 3. Estruturação de Pastas Conforme Arquitetura em Camadas

O template oficial cria uma estrutura genérica (`src/game/scenes`). Você deve reorganizá-la para atender à **Seção 19.4** do documento de visão. Execute os comandos abaixo na raiz do projeto:

```bash
# Cria camadas de domínio, serviços, dados e conteúdo
mkdir -p src/domain src/services src/data src/content src/ui

# Move cenas para a pasta correta (ajuste se o template criou src/game/scenes)
# Se o template usou src/game/scenes, mova para src/scenes:
mv src/game/scenes src/scenes 2>/dev/null || true

# Remove pasta game genérica se estiver vazia após a movimentação
rm -rf src/game 2>/dev/null || true

# Cria pastas de assets organizadas (Seção 18.5)
mkdir -p public/assets/images/characters
mkdir -p public/assets/images/tiles
mkdir -p public/assets/images/ui
mkdir -p public/assets/audio/music
mkdir -p public/assets/audio/sfx
mkdir -p public/assets/fonts
mkdir -p docs/licenses
```

### 4. Configuração do Vite para Phaser 3 e Persistência Local

Edite o arquivo `vite/config.*.mjs` (ou `vite.config.js`) para garantir que o build funcione offline (RNF-04) e que o localStorage seja acessível corretamente no Electron:

```javascript
import { defineConfig } from 'vite';

export default defineConfig({
    base: './', // Garante caminhos relativos para funcionar em file:// e Electron
    build: {
        target: 'es2015', // Compatível com navegadores mais antigos em PCs escolares
        outDir: 'dist',
        assetsInlineLimit: 0 // Força assets a serem arquivos separados (melhor para cache)
    },
    server: {
        port: 8080,
        open: true
    }
});
```

### 5. Scripts NPM Alinhados ao Fluxo de Desenvolvimento

Atualize a seção `"scripts"` do seu `package.json` para incluir os comandos de teste e build desktop previstos no GDD:

```json
"scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "test": "vitest run",
    "test:watch": "vitest",
    "electron:dev": "vite build && electron .",
    "electron:build": "vite build && electron-builder"
}
```

### 6. Validação Inicial na ThinkPad T420

Antes de escrever qualquer lógica de jogo, valide que o ambiente funciona na sua GPU Intel HD 3000:

```bash
npm run dev
```

Abra `http://localhost:8080` e verifique:
-   Se o canvas renderiza sem erros no console.
-   Se não houver renderização, force o modo Canvas no `src/main.js`:
    ```javascript
    const config = {
        type: Phaser.CANVAS, // Fallback seguro para Intel HD 3000
        width: 1280,
        height: 720,
        // ... demais configs
    };
    ```

### 7. Próximo Passo Imediato (Fluxo Vertical da Sprint 1)

Conforme a **Seção 30.1** do GDD, seu primeiro objetivo técnico é:
> Criar perfil → iniciar protótipo da Fase 1 → interagir com um objeto → escolher uma ação → atualizar conduta → salvar → fechar → abrir → retomar.

Comece implementando `src/data/LocalSaveRepository.js` e `src/domain/ScoreService.js` **antes** das cenas, pois eles são independentes de Phaser e testáveis com Vitest. Isso evita o risco R-02 (integração tardia).

## Configuração Avançada e Diagnóstico da Acoplhadora Gráfica (GPU)

Após a configuração do ambiente de desenvolvimento e a criação da estrutura do projeto, o obstáculo mais provável e técnico surge: a interação com o hardware gráfico específico da máquina, a Intel HD Graphics 3000. Esta GPU, embora capaz de executar tarefas básicas, apresenta limitações significativas que podem levar a problemas de compatibilidade, desempenho e estabilidade com tecnologias web modernas como o WebGL. Esta secção aborda proativamente esses desafios, fornecendo um guia detalhado sobre a diagnóstico do estado da GPU, a otimização do ambiente de software para maximizar o desempenho e as estratégias para contornar as limitações do hardware, garantindo assim que o jogo Phaser seja executado da forma mais fiel possível.

A Intel HD Graphics 3000, lançada em 2011, é uma GPU integrada da arquitetura Sandy Bridge [[68](https://github.com/pal1000/mesa-dist-win/discussions/171)]. As suas capacidades são um fator crítico nesta configuração. Ela suporta o OpenGL até à versão 4.0 ou 4.1 [[57](https://news.ycombinator.com/item?id=14181897)], o que significa que ela é tecnicamente capaz de executar o WebGL 1.0, o padrão básico para renderização em navegadores [[10](https://www.facebook.com/groups/1173611376046323/posts/34629712923342738/)]. No entanto, ela não atende aos requisitos mínimos para o WebGL 2.0, que exige funcionalidades mais avançadas do hardware [[77](https://groups.google.com/g/webgl-dev-list/c/SzTfCzoB5Ss)]. O Phaser 3 e versões posteriores são projetados para tirar partido de funcionalidades modernas, e embora o framework tente utilizar WebGL por defeito, a instabilidade ou a ausência completa deste recurso na HD Graphics 3000 é uma probabilidade elevada. Adicionalmente, a GPU pode ter problemas de desempenho devido a sobrecarga térmica e consumo de energia, levando a um throttling que resulta em baixos FPS [[65](https://phaser.discourse.group/t/how-to-deal-with-integrated-graphics-low-performance-webgl/15374)].

Antes de tentar resolver qualquer problema, é imperativo diagnosticar o estado real da aceleração gráfica no sistema. Os navegadores modernos fornecem painéis de depuração detalhados para este fim.

No Google Chrome e Chromium, a página mais importante é `chrome://gpu`. Para aceder a ela, basta abrir um novo separador e digitar o endereço. Esta página revela uma avalanche de informações, mas os campos mais importantes a monitorizar são:
*   **"Graphics Feature Status"**: Esta secção resume o status de cada característica gráfica. Procure por entradas como "WebGL", "WebGL2" e "Compositing". Se alguma destas for marcada como "Disabled", "Software only" ou "Hardware accelerated but blocked for an unknown reason", isso indica um problema subjacente [[6](https://forum.vivaldi.net/topic/109553/hardware-acceleration-is-not-working-on-ubuntu), [100](https://forums.linuxmint.com/viewtopic.php?t=462816)]. Uma mensagem como "WebGL has been disabled via blocklist or the command line" é um sinal claro de que o driver ou a GPU foram bloqueados por alguma razão [[6](https://forum.vivaldi.net/topic/109553/hardware-acceleration-is-not-working-on-ubuntu), [100](https://forums.linuxmint.com/viewtopic.php?t=462816)].
*   **"Video Decode" e "Video Encode"**: Embora não diretamente relacionados com o Phaser, a desativação destes codecs pode ser um sintoma de problemas mais profundos com os drivers [[100](https://forums.linuxmint.com/viewtopic.php?t=462816)].
*   **"Driver Information"**: Confira a versão do driver. Drivers muito antigos podem causar falhas de compatibilidade [[7](https://groups.google.com/g/webgl-dev-list/c/19RIf8GZNVg)].

No Mozilla Firefox, o diagnóstico ocorre em `about:support`. Na página que se abre, procure pela secção "Informações de Aplicação" e expanda-a. Procure pelos campos "WebGL 1 Driver Renderer" e "WebGL 2 Driver Renderer". O valor desses campos mostrará qual driver está a ser utilizado para a renderização. Se os valores forem genéricos como "llvmpipe" (um software rasterizer) ou se a renderização for reportada como "Offscreen", isso indica que a aceleração de hardware não está a ser usada [[13](https://support.mozilla.org/en-US/questions/1562326), [94](https://bbs.archlinux.org/viewtopic.php?id=254933)].

Se o diagnóstico revelar que a aceleração de hardware não está a ser utilizada ou é instável, a primeira linha de ação deve ser a atualização dos drivers gráficos. No Linux, a pilha de drivers para GPUs Intel é a Mesa, um conjunto de bibliotecas de código aberto que implementam APIs como OpenGL, Vulkan e EGL [[55](https://www.youtube.com/watch?v=X-qCK2vYtzo), [97](https://www.youtube.com/watch?v=aUXaIgSGVjE)]. A versão do Mesa incluída nos repositórios padrão do Ubuntu 24.04 pode não conter as correções de bugs mais recentes para GPUs antigas como a HD Graphics 3000 [[67](https://bugs.launchpad.net/bugs/2120238)]. A solução mais eficaz para muitos problemas de compatibilidade é atualizar para uma versão mais recente do Mesa. Uma fonte confiável para isto é o Personal Package Archive (PPA) Kisak, que fornece builds estáveis e testados do Mesa [[96](https://linuxcapable.com/how-to-upgrade-mesa-drivers-on-linux-mint/)].

O processo para adicionar o PPA Kisak e atualizar o Mesa é o seguinte:
1.  Primeiro, certifique-se de que o sistema está atualizado e instale as ferramentas necessárias:
    ```bash
    sudo apt update && sudo apt upgrade -y
    sudo apt install -y mesa-utils software-properties-common
    ```
2.  Adicione o PPA Kisak ao seu sistema:
    ```bash
    sudo add-apt-repository ppa:kisak/kisak-mesa
    ```
3.  Atualize a lista de pacotes e realize uma atualização completa do sistema, que incluirá a nova versão do Mesa:
    ```bash
    sudo apt update
    sudo apt full-upgrade
    ```

Após a atualização, é aconselhável reiniciar o computador e verificar novamente a versão do Mesa com o comando `glxinfo | grep "OpenGL version"` para confirmar a mudança [[96](https://linuxcapable.com/how-to-upgrade-mesa-drivers-on-linux-mint/)]. Esta atualização de drivers é, muitas vezes, a solução definitiva para problemas de WebGL em GPUs Intel antigas [[95](https://forums.sketchup.com/t/still-broken-on-linux/219358)].

Se a atualização dos drivers não resolver o problema, ou se o WebGL continuar a ser instável, a próxima etapa é verificar as configurações do próprio navegador. Em ambos o Chrome e o Firefox, a aceleração de hardware precisa ser explicitamente ativada.
*   **No Chrome**, vá para `Configurações > Sistema` e certifique-se de que a opção "Utilizar aceleração de gráficos sempre que disponível" está ativada [[11](https://www.reddit.com/r/FoundryVTT/comments/o5wou1/error_message_your_browser_does_not_support_webgl/), [64](https://github.com/photopea/photopea/issues/7363)]. Após mudar esta definição, é obrigatório reiniciar o navegador.
*   **No Firefox**, a situação é diferente. A aceleração de hardware é frequentemente desativada por defeito em sistemas Linux como uma medida de segurança para evitar problemas com drivers problemáticos [[106](https://www.reddit.com/r/linux/comments/3g07sn/til_you_have_to_force_firefox_to_use_hardware/)]. Para a ativar, vá para `about:config`, procure por `layers.acceleration.force-enabled` e defina-o como `true`. Note que esta é uma solução drástica e pode não funcionar se houver um problema fundamental com o driver.

Como último recurso, caso o WebGL continue a falhar, pode ser necessário forçar o jogo Phaser a usar o renderizador Canvas. O Phaser possui um mecanismo de fallback automático (`type: Phaser.AUTO`), mas este pode não ser robusto o suficiente. Um desenvolvedor pode programaticamente forçar o uso do Canvas, o que garante que o jogo irá executar, embora com algumas restrições. Para fazer isto, edite o ficheiro `src/main.js` do projeto. Localize o objeto de configuração do jogo e altere a propriedade `type`. Em vez de `Phaser.AUTO`, defina-a como `Phaser.CANVAS`:

```javascript
const config = {
    type: Phaser.CANVAS, // Força o uso do renderizador Canvas
    width: 800,
    height: 600,
    scene: {
        preload: preload,
        create: create
    }
};

new Phaser.Game(config);
```
Forçar o Canvas resolve problemas de compatibilidade com o hardware, mas é crucial entender as implicações. Múltiplas fontes alertam que certas funcionalidades do Phaser são exclusivas do renderizador WebGL e não funcionarão ou terão comportamentos incorretos no Canvas. Exemplos incluem a tintagem de sprites (`sprite.setTint()`), certos efeitos pós-processamento como o *glow*, e algumas funcionalidades do sistema de gráficos [[38](https://phaser.discourse.group/t/features-that-do-not-support-canvas-mode/8951), [40](https://franzeus.medium.com/how-i-optimized-my-phaser-3-action-game-in-2025-5a648753f62b), [66](https://phaser.discourse.group/t/tinting-an-image-no-longer-works-in-firefox/14477)]. Uma fonte sugere que a tintagem é uma funcionalidade que não funciona no renderizador Canvas [[66](https://phaser.discourse.group/t/tinting-an-image-no-longer-works-in-firefox/14477)]. Outras inconsistências foram relatadas, como problemas com a transparência de cores ao usar `fillStyle()` após `fillRectShape()` [[38](https://phaser.discourse.group/t/features-that-do-not-support-canvas-mode/8951)]. Portanto, forçar o Canvas deve ser visto como uma solução de último recurso, e o desenvolvedor deve estar ciente de que algumas funcionalidades visuais planeadas podem ter de ser reescritas ou abandonadas.

Finalmente, uma otimização do ambiente de software pode proporcionar um aumento de desempenho significativo. O ThinkPad T420 está a executar o ambiente gráfico XFCE . O XFCE é notoriamente leve, mas o seu gestor de janelas, xfwm4, inclui um compositor que pode introduzir uma sobrecarga de CPU/GPU desnecessária em aplicações gráficas intensivas como jogos [[82](https://www.reddit.com/r/linux_gaming/comments/1eqqsb8/disabling_compositor_in_xfce4_results_in_3040/)]. Desativar o compositor pode resultar num aumento de desempenho de 30-40% ou até dobrar os FPS em alguns casos [[82](https://www.reddit.com/r/linux_gaming/comments/1eqqsb8/disabling_compositor_in_xfce4_results_in_3040/), [101](https://forums.developer.nvidia.com/t/580-release-feedback-discussion/341205/944)]. Para desativar o compositor no XFCE 4.18, vá para `Configurações do Ambiente de Trabalho > Ajustes do Gestor de Janelas > Compositor` e desmarque a caixa de verificação "Ativar o compositor de ecrã" [[101](https://forums.developer.nvidia.com/t/580-release-feedback-discussion/341205/944)]. Esta é uma alteração global que afeta toda a interface do utilizador, mas pode ser revertida facilmente se o desempenho do jogo for a prioridade máxima.

Em síntese, o sucesso na execução de um jogo Phaser na Intel HD Graphics 3000 depende de um processo de diagnóstico e resolução de problemas sistemático. Começando pela atualização dos drivers Mesa, passando pela verificação das configurações de aceleração de hardware do navegador e culminando na disposição de forçar o renderizador Canvas como último recurso, o desenvolvedor pode superar as limitações do hardware e garantir que o projeto seja executado com sucesso.

## Execução do Projeto e Fluxo de Trabalho de Desenvolvimento

Com o ambiente de desenvolvimento configurado, o projeto Phaser estruturado e as potenciais armadilhas da aceleração gráfica devidamente abordadas, o foco agora desloca-se para a execução prática do jogo e a adoção de um fluxo de trabalho de desenvolvimento eficiente. Esta secção detalha os passos para iniciar o servidor de desenvolvimento, visualizar o jogo no navegador e explorar as funcionalidades do Vite que aceleram o ciclo de iteração, transformando a fase de codificação de uma tarefa sequencial e demorada numa experiência dinâmica e interativa.

O ponto de partida para a execução do projeto é a utilização dos scripts de utilidade definidos no ficheiro `package.json`, que são gerados automaticamente pelo template do Phaser. O Vite, por sua vez, utiliza estes scripts para orquestrar as tarefas comuns de desenvolvimento. O comando mais importante para o desenvolvimento iterativo é o seguinte:

```bash
npm run dev
```

Esta instrução diz ao npm para procurar e executar o script chamado `dev` dentro do objeto `scripts` do `package.json`. No template Vite do Phaser, este script invoca o binário do Vite para iniciar o servidor de desenvolvimento. Ao executar este comando num terminal dentro do diretório raiz do projeto, o Vite realizará as seguintes ações:
1.  **Análise do Projeto:** Ele irá analisar a árvore de dependências do projeto, identificando os módulos ESM e as suas interdependências.
2.  **Início do Servidor:** Ele iniciará um servidor HTTP local, tipicamente em `http://localhost:5173` (a porta pode variar). Este servidor não apenas servirá o ficheiro `public/index.html`, mas fará-o de uma forma inteligente, injetando um pequeno script de proxy que ativa o Hot Module Replacement (HMR).
3.  **Exibição no Terminal:** O terminal mostrará a URL do servidor, as portas utilizadas e quaisquer avisos ou erros durante a inicialização.

Uma vez que o servidor de desenvolvimento esteja a funcionar, abra um navegador web da sua escolha (Google Chrome, Firefox, etc.) e navegue para o endereço indicado (ex: `http://localhost:5173`). Se tudo estiver configurado corretamente, deverá ver uma tela de fundo azul com o texto "Boas-vindas ao Phaser com Vite!".

A principal vantagem do fluxo de trabalho com Vite é o HMR. O HMR permite que as alterações realizadas nos ficheiros de origem sejam comunicadas para o navegador em tempo real, sem a necessidade de uma recarga completa da página. Quando o HMR está ativado, o estado da aplicação (incluindo o estado dos objetos no jogo) é preservado, o que é extremamente valioso para o desenvolvimento de jogos, onde perder o estado de um nível ou de uma cena pode ser frustrante e demorado. Para experimentar isto, modifique o ficheiro `src/scenes/Scene.js`. Altere a cor de fundo na função `create()` de `0x0074D9` para outra cor, como `0xff0000` (vermelho). Guarde o ficheiro. O navegador irá atualizar instantaneamente, mostrando a nova cor de fundo, e você poderá ver a alteração refletida no jogo sem perder o contexto da sua sessão de desenvolvimento.

É importante notar que, embora o Vite resolva a maioria dos problemas de CORS (Cross-Origin Resource Sharing) associados ao uso do protocolo `file://`, é ainda uma boa prática servir o projeto através do seu próprio servidor. Servir ficheiros diretamente do sistema de ficheiros (`file://`) pode levar a problemas de CORS ao carregar recursos como imagens, áudio ou dados JSON, especialmente em ambientes de desenvolvimento locais [[49](https://dev.to/dengel29/loading-local-files-in-firefox-and-chrome-m9f), [79](https://stackoverflow.com/questions/10752055/cross-origin-requests-are-only-supported-for-http-error-when-loading-a-local)]. O servidor do Vite opera em `http://localhost`, tratando adequadamente estas requisições e eliminando um campo fértil para erros difíceis de depurar.

Durante o desenvolvimento, o Phaser fornece uma série de ferramentas de depuração integradas. Por exemplo, para visualizar as áreas delimitadoras (bounds) de todos os sprites na cena, pode adicionar a seguinte linha à função `create()` do seu ficheiro `Scene.js`:

```javascript
this.sys.game.renderer.debug.displayList = true;
```

Isto ativará a sobreposição de informações de depuração no canvas, mostrando retângulos ao redor de cada sprite renderizado. Outras ferramentas de depuração incluem a visualização de physics bodies, tilemap layers e muito mais, todas documentadas na API do Phaser [[54](https://phaser.io/news/2026/07/phaser-4-tutorial-project-setup-core-concepts)].

Outro aspeto crucial do fluxo de trabalho é a gestão de assets (ativos). Embora o template inicial venha com um logótipo do Phaser, o seu jogo real exigirá sprites, som, música, fontes e dados de tilemaps. A melhor prática é organizar estes ativos numa pasta dedicada, por exemplo, `src/assets`. O Vite é configurado para servir automaticamente o conteúdo do diretório `public`, então colocar os ativos lá é uma opção simples. No entanto, para uma organização mais limpa, é comum manter os ativos na pasta `src` e importá-los diretamente nos seus ficheiros JavaScript/TypeScript. O Vite lida com a importação de ficheiros de imagem, áudio, etc., tratando-os como módulos cujo URL final pode ser usado como a fonte de um recurso.

Por exemplo, para carregar uma imagem, o procedimento típico em Phaser seria:
1.  Colocar a imagem (ex: `player.png`) na pasta `src/assets/images`.
2.  Importar a imagem no seu ficheiro de cena:
    ```javascript
    import playerImage from '../assets/images/player.png';
    ```
3.  Usar o caminho da imagem importada na função `preload()` do Phaser:
    ```javascript
    preload() {
        this.load.image('player', playerImage);
    }
    ```
4.  Utilizar a imagem carregada na função `create()`:
    ```javascript
    create() {
        this.add.sprite(400, 300, 'player');
    }
    ```

Este fluxo de trabalho modular, facilitado pelo Vite, torna a gestão de ativos mais robusta e integrada com o resto do processo de construção da aplicação.

O desenvolvimento de jogos raramente segue uma linha reta. É um processo iterativo de teste, erro e refinamento. O servidor de desenvolvimento do Vite, com o seu HMR rápido e servidor de proxies, é o motor que impulsiona este ciclo. Permite que o desenvolvedor se concentre na lógica do jogo e na experiência do jogador, em vez de ficar preso no ciclo tedioso de "salvar, recarregar, esperar". A combinação de um template Phaser bem configurado com o Vite cria um ambiente de desenvolvimento que é ao mesmo tempo poderoso e intuitivo, preparando o terreno para a criação de experiências de jogo interessantes e complexas.

## Construção e Distribuição do Projeto de Jogo

O ciclo de desenvolvimento de um jogo não termina quando o jogo está a funcionar corretamente no ambiente de desenvolvimento local. A fase final e crucial é a preparação do jogo para ser distribuído e executado por outros, seja através de um site web ou de um pacote de desktop. Esta secção aborda o processo de construção de uma versão otimizada e de produção do projeto Phaser, explicando como o Vite automatiza este processo e como a estrutura de ficheiros resultante deve ser servida. Este é o passo que transforma o código de desenvolvimento legível por humanos em uma aplicação web performática e compacta.

No mundo do desenvolvimento web moderno, uma "build de produção" não é apenas uma cópia dos ficheiros originais. Envolve várias otimizações que melhoram o desempenho de carregamento e execução do jogo em um ambiente de produção, onde a rapidez é primordial. Estas otimizações incluem a minificação do código JavaScript e CSS (removendo espaços em branco e comentários, e renomeando variáveis para nomes curtos), a otimização de imagens, e a análise e separação do código em pacotes menores para carregamento assíncrono. Realizar estas otimizações manualmente seria um processo longo e propenso a erros. Felizmente, ferramentas como o Vite automatizam todo este processo de forma eficiente.

O Vite vem com um script de construção pré-configurado no `package.json` do template Phaser. Para gerar a build de produção, basta executar o seguinte comando no terminal, a partir do diretório raiz do projeto:

```bash
npm run build
```

Este comando instrui o Vite a analisar o projeto em modo de produção. Diferentemente do modo de desenvolvimento, o modo de produção realiza todas as otimizações mencionadas anteriormente. O Vite processará todos os módulos JavaScript, consolidará-os em um ou mais ficheiros minimizados, otimizará os ativos e, em seguida, gerará uma versão final do projeto.

Após a conclusão bem-sucedida do comando `npm run build`, o Vite criará um novo diretório chamado `dist` no diretório raiz do projeto. Este diretório contém toda a artefatos da build de produção. A estrutura de ficheiros dentro de `dist` será a seguinte:
*   **`assets/`**: Este diretório contém os ficheiros de código otimizados. Dentro dele, você encontrará um ficheiro de JavaScript principal (geralmente algo como `assets/_assets.main-XXXXXX.hash.js`) e um ficheiro de CSS (se houver estilos globais). O nome do ficheiro de JavaScript inclui um hash único, uma técnica de cache busting que força os navegadores a baixarem uma nova versão do ficheiro sempre que o seu conteúdo mudar.
*   **`index.html`**: Este é o ficheiro HTML otimizado e pronto para produção. Ele já contém as referências corretas aos ficheiros JavaScript e CSS otimizados dentro da pasta `assets/`. Todos os caminhos são relativos e absolutamente corretos para funcionar no servidor.
*   **Outros ficheiros de ativos**: Qualquer outra imagem, áudio ou outro ficheiro que tenha sido colocado na pasta `public` será copiado para a raiz do diretório `dist` ou para subpastas correspondentes.

Este diretório `dist` é o produto final do processo de construção. Ele contém tudo o que é necessário para servir o jogo. Não há necessidade de distribuir os ficheiros de origem (`src/`), as dependências de desenvolvimento (`node_modules/`) ou os ficheiros de configuração. A simplicidade desta entrega é uma vantagem significativa do fluxo de trabalho baseado em bundlers.

Servir o conteúdo do diretório `dist` é tão simples quanto servir qualquer conjunto de ficheiros estáticos. Existem várias formas de fazer isso:
1.  **Um Servidor HTTP Simples:** Se você apenas quiser testar a build de produção localmente, pode usar um servidor HTTP simples. O Python, por exemplo, vem com um módulo `http.server` que pode servir a pasta `dist` instantaneamente. Basta navegar para o diretório `dist` num terminal e executar:
    ```bash
    python -m http.server 8080
    ```
    Em seguida, abra o navegador em `http://localhost:8080` [[128](https://askubuntu.com/questions/1102594/how-do-i-set-up-the-simplest-http-local-server), [130](https://www.digitalocean.com/community/tutorials/python-simplehttpserver-http-server)]. Esta é uma forma rápida de validar que a build foi criada corretamente e que o jogo funciona como esperado fora do ambiente de desenvolvimento do Vite.

2.  **Um Servidor Web Real:** Para distribuição pública, o conteúdo do diretório `dist` deve ser implantado num servidor web tradicional, como o Apache ou o Nginx. Você simplesmente aponta a diretiva `DocumentRoot` do seu servidor para o diretório `dist`. O servidor web irá tratar de servir os ficheiros `index.html`, JavaScript, CSS e ativos de forma eficiente a todos os visitantes.

3.  **Plataformas de Hospedagem Estática:** Muitas plataformas modernas de hospedagem de sites estáticos, como Netlify, Vercel, GitHub Pages ou AWS S3, são otimizadas para servir exatamente este tipo de artefacto. Elas podem pegar o diretório `dist` e publicá-lo com uma URL pública em questão de minutos, muitas vezes com funcionalidades adicionais como CDN (Rede de Entrega de Conteúdo) integrada para acelerar o carregamento em todo o mundo.

É importante notar que, embora o processo de construção seja automatizado, o desenvolvedor ainda tem controlo sobre o que acontece durante a construção. O ficheiro `vite.config.js` pode ser editado para adicionar plugins personalizados, modificar as opções de construção do Vite (por exemplo, controlar o baldeamento de módulos, a geração de índices, etc.), ou até mesmo integrar-se com outras ferramentas de otimização de ativos. No entanto, para a grande maioria dos projetos Phaser, as configurações predefinidas do Vite são mais do que suficientes.

Em suma, o processo de construção e distribuição é o fechamento do ciclo de vida do desenvolvimento. Através do comando `npm run build`, o Vite transforma o projeto de desenvolvimento em uma aplicação web otimizada e pronta para o consumo. O diretório `dist` resultante é uma coleção autocontida de artefactos estáticos que podem ser servidos por qualquer servidor web padrão. Este fluxo de trabalho simplificado e altamente otimizado permite que os desenvolvedores de jogos foquem-se na criação de conteúdo e na experiência do jogador, sabendo que a entrega final será rápida, eficiente e profissional.
