## Estrutura
- O arquivo `cef.asi` deve estar na pasta raiz do jogo (gerado como `loader.dll`).
- A pasta `cef` com todo o conteúdo também deve estar lá.
- Também é criada uma pasta `CEF` no caminho `Meus documentos/GTA San Andreas User Files/CEF/`, onde o cache, cookies e outros itens necessários para o funcionamento correto do Chromium são armazenados.
- `gta_sa.exe`
- `cef.asi`
- `cef/`
    - `client.dll`
    - `libcef.dll`
    - `renderer.exe`
    - etc...

## Dicas de uso e algumas restrições
- Idealmente, tenha um único navegador com todas as interfaces. Não crie novos navegadores para diferentes ações, use o sistema de eventos embutido.
- Se você tiver plugins do cliente que usam caminhos relativos, provavelmente eles não funcionarão corretamente. Infelizmente, no momento da inicialização, o diretório atual é alterado em outra thread. Como exemplo: a biblioteca CLEO pode criar seu próprio log `cleo.log`, bem como as pastas `cleo_text` e `cleo_saves` na pasta `cef`. Para um funcionamento correto, é melhor obter o caminho para o arquivo executável atual (`gta_sa.exe`).

## API Pawn

`cef_create_browser(player_id, browser_id, const url[], hidden, focused)`

Cria um navegador para o jogador especificado.

`cef_destroy_browser(player_id, browser_id)`

Remove o navegador.

`cef_hide_browser(player_id, browser_id, hide)`

Oculta o navegador.

`cef_emit_event(player_id, const event_name[], args...)`

Aciona um evento no cliente. Tipos de argumentos suportados: `string`, `integer`, `float`.

`cef_subscribe(const event_name[], const callback[])`

Se inscreve para um evento do cliente. Assinatura do callback da função: `Callback(player_id, const arguments[])`

`cef_player_has_plugin(player_id)`

Verifica se o cliente possui o plugin.

`cef_create_ext_browser(player_id, browser_id, const texture[], const url[], scale)`

Cria um navegador como no primeiro caso, mas marcado para ser exibido em objetos em uma textura específica. O parâmetro `scale` indica quanto a textura padrão deve ser ampliada. Por exemplo, se a textura padrão tiver o tamanho `250x30`, ela terá o tamanho `1250x150` com o parâmetro 5.

`cef_append_to_object(player_id, browser_id, object_id)`

Substitui a textura do objeto fornecido pela imagem do navegador no cliente. O navegador deve ser criado com `cef_create_ext_browser`, e a textura especificada na criação deve estar presente para exibição correta.

`cef_remove_from_object(player_id, browser_id, object_id)`

Restaura a textura original do objeto.

`cef_toggle_dev_tools(player_id, browser_id, enabled)`

Ativa/desativa as ferramentas de desenvolvedor.

`native cef_set_audio_settings(player_id, browser_id, Float:max_distance, Float:reference_distance)`

Define a distância máxima audível para o navegador no objeto. `reference_distance` é a distância em que o volume será máximo, diminuindo de `max_distance` até 0.

`cef_focus_browser(player_id, browser_id, focused)`

Função para focar o navegador. Ele fica em primeiro plano, recebe todos os eventos de teclado e mouse. O mesmo que passar o argumento `focused = true` ao criar o navegador.

`cef_always_listen_keys(player_id, browser_id, listen)`

Permite que o navegador receba entradas de teclado em segundo plano, mesmo que ele não esteja focado ou oculto. Isso permite o uso de funções para eventos de teclado em código JS o tempo todo. Por exemplo, abrir/fechar a interface ao pressionar uma tecla (`window.addEventListener("keyup")`).

`cef_load_url(player_id, browser_id, const url[])`

Carrega a URL especificada no navegador dado. Mais rápido do que recriar o navegador.

### Há também dois eventos incorporados no plugin:

`forward OnCefBrowserCreated(player_id, browser_id, status_code)`
É acionado quando o cliente cria um navegador a pedido do servidor / plugin. O valor de `status_code` é 0 (para criação malsucedida) ou o código HTTP (200, 404 etc).

`forward OnCefInitialize(player_id, success)`
É acionado após o cliente se conectar ao servidor CEF, ou quando o tempo limite é atingido. Basicamente, substitui a verificação manual `cef_player_has_plugin`.

## API do Navegador

Os navegadores também têm sua própria API para gerenciá-los.

`cef.set_focus(focused)`

Foca no navegador, permitindo que ele esteja acima de todas as outras janelas e tenha a capacidade de receber entrada de teclado e mouse.

`cef.on(event_name, callback)`

Inscreve-se em um evento do navegador / outros plugins.

`cef.off(event_name, callback)`

NÃO FUNCIONA NESTE MOMENTO
Cancela a inscrição em um evento. Para usar essa função, você precisa passar a variável que contém a função de retorno que foi usada ao se inscrever no evento.

`cef.hide(hide)`

Oculta o navegador e desativa o som dele.

`cef.emit(event_name, args...)`

Aciona um evento no servidor / plugins externos com os argumentos especificados. Suporta todos os tipos, exceto objetos com campos e funções. Observação: em plugins, todos os argumentos podem ser usados de maneira humana, mas no servidor, todos os argumentos são convertidos em uma única string separada por espaços.

## API C

Não está funcionando, desculpe.

Exemplo funcional em `cef-interface`, além da API em `cef-api` e `client/external.rs`

```C++
    #include <cstdint>
    
    // Отменить продолжение выполнения колбеков события. А так же не отправлять его серверу.
    static const int EXTERNAL_BREAK = 1;
    // Продолжить выполнение. Если никто не отменил его, будет передано серверу.
    static const int EXTERNAL_CONTINUE = 0;
    
    using BrowserReadyCallback = void(*)(uint32_t);
    using EventCallback = int(*)(const char*, cef_list_value_t*);
    
    extern "C" {
        // Проверка на существование браузера в игре.
        bool cef_browser_exists(uint32_t browser);
        // Создан ли браузер и загружен веб-сайт.
        bool cef_browser_ready(uint32_t browser);
        // Создать браузер с указанными параметрами. Эта функция асинхронная, браузер создается не сразу.
        void cef_create_browser(uint32_t id, const char *url, bool hidden, bool focused);
        // Создать CefListValue внутри клиента.
        cef_list_value_t *cef_create_list();
        // Удалить браузер у клиента.
        void cef_destroy_browser(uint32_t id);
        // Вызвать событие у браузера.
        void cef_emit_event(const char *event, cef_list_value_t *list);
        // Сфокусировать ввод на браузере, а так же вывести его поверх всех остальных.
        void cef_focus_browser(uint32_t id, bool focus);
        // Активно ли окно игры.
        bool cef_gta_window_active();
        // Скрыть браузер.
        void cef_hide_browser(uint32_t id, bool hide);
        // Проверить доступен ли ввод для конкретного браузера.
        bool cef_input_available(uint32_t browser);
        // Подписаться на событие полного создания браузера.
        void cef_on_browser_ready(uint32_t browser, BrowserReadyCallback callback);
        bool cef_ready();
        // Подписаться на события от браузера.
        void cef_subscribe(const char *event, EventCallback callback);
        // Попытаться сфокусироваться на браузере. Аналогично паре `cef_input_available` + `cef_focus_browser`,
        // но с одним значительным условием, между выполнением этих двух функции кто-то другой может захватить фокус.
        // А данная функция атомарна, что позволяет проверить и сразу же захватить, гарантируя,
        // что никто другой не сможет в это время получить фокус.
        bool cef_try_focus_browser(uint32_t browser);
    }
```

## Instruções de Uso

### Descrição
Um navegador pode ser criado de dois lugares: a partir de um mod de jogo e plugins.

Um navegador tem dois estados adicionais: `hidden` (oculto) e `focused` (focado). O primeiro estado controla a exibição do navegador na tela do jogador. O segundo estado é mais complexo: quando o navegador está focado (`focused = true`), a câmera do jogador fica congelada, um cursor aparece e todas as entradas do jogador (do teclado e do mouse) são direcionadas diretamente para o navegador, ignorando o GTA e o SA:MP (com exceção de algumas funções como captura de tela com F8). O jogador não pode sair desse estado por conta própria, você deve permitir isso no código da interface do navegador. Por exemplo, você pode