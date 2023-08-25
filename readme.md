# SAMP CEF - Integração de Interfaces Web para SA:MP

O projeto SAMP CEF permite incorporar a funcionalidade do Chromium Embedded Framework (CEF) no SA:MP (San Andreas Multiplayer), possibilitando a criação de interfaces de jogo atraentes usando HTML, CSS e JavaScript.

**IMPORTANTE:** Este projeto é um **FRAMEWORK** (ou SDK) e não um software pronto para uso. Para criar interfaces, é necessário ter conhecimento básico em desenvolvimento web (JS / HTML / CSS).

## Recursos

- Crie visualizações de navegador a partir de um gamemode ou plugins do lado do cliente (C ABI).
- Coloque navegadores em objetos do jogo, inclusive com simulação de som espacial.
- Envie e receba eventos personalizados entre os clientes e o servidor.

## Pacotes Principais

- `cef` - Wrappers em Rust para a API CEF em C.
- `cef-api` - Wrappers em Rust para construir plugins do cliente usando CEF.
- `cef-interface` - Exemplo de um plugin em Rust.
- `cef-sys` - Bindings para a API CEF em C.
- `client` - Plugin do lado do cliente para CEF.
- `d3dx9` - Bindings para o SDK do DirectX.
- `loader` - Carregador necessário para funcionamento (deve ser nomeado como `cef.asi`).
- `messages` - Mensagens protobuf para comunicação na rede.
- `network` - Integração com o Quinn (similar ao Laminar).
- `proto` - Arquivos de protótipo crus.
- `renderer` - Conexão entre o processo de renderização do CEF e a lógica principal.
- `server` - Plugin do lado do servidor.

## Download

As versões mais recentes estão disponíveis [nas Actions](https://github.com/ZOTTCE/samp-cef/actions) para diferentes sistemas operacionais:

- CentOS 7 (`cef-centos-7.so`)
- Debian 9, 10, 11 (`cef-debian-*.so`)
- Ubuntu 18.04, 20.04 (`cef-ubuntu-*.so`)
- Windows (`cef-windows.dll`)

Além disso, estão disponíveis todas as partes do lado do cliente (`cef.asi`, `client.dll`, `renderer.exe`).

## Compilação

### Dependências

- [Compilador Rust (nightly) com a ferramenta `i686-windows-pc-msvc`](https://rust-lang.org)
- CEF pré-compilado com códigos proprietários (para uso de streams). Versões estão disponíveis nas releases.
- Variável de ambiente `CEF_PATH` que aponte para `libcef.lib` (apenas para cliente).

### Executando a Compilação

```sh
rustup toolchain install nightly-i686
cargo +nightly-i686 build --release
```

## Versão do CEF

Versões atuais do CEF e Chromium: `89.0.5+gc1f90d8+chromium-89.0.4389.40`

## Documentação

- [docs/main_ru.md](/docs/main_ru.md)
- [docs/main_en.md](/docs/main_en.md)

## Exemplos em Vídeo

Confira exemplos de uso no [YouTube](https://www.youtube.com/user/colgatekek).

## Observação

Este projeto tem semelhanças com implementações do FiveM e RAGE Multiplayer.
