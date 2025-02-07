# SSD1306 I2C Display Driver

Este código é a modificação de um driver para o display OLED SSD1306 utilizando a interface I2C em um Raspberry Pi Pico W, com foco em funcionalidades de controle de exibição, como desenhar caracteres, linhas, e strings no display.

## Funções

Aqui está uma lista da modificações que fiz.

### 1. `ssd1306_draw_char_absolute`
Desenha um caractere na posição exata, independentemente de sobreposição de pixels.

**Parâmetros:**
- `ssd`: Buffer de dados do display.
- `x`, `y`: Posições de coordenadas para o caractere.
- `character`: Caractere a ser desenhado.

### 2. `ssd1306_draw_string_absolute`
Desenha uma string de caracteres na posição exata, ignorando páginas de memória e sobrepondo os caracteres, se necessário.

**Parâmetros:**
- `ssd`: Buffer de dados do display.
- `x`, `y`: Posições de coordenadas para o início da string.
- `string`: String de caracteres a ser desenhada.

### 3. `ssd1306_draw_string_with_word_wrap`
Desenha uma string com quebra automática de linha, garantindo que as palavras não ultrapassem a largura da tela.

**Parâmetros:**
- `ssd`: Buffer de dados do display.
- `x`, `y`: Posições de coordenadas para o início da string.
- `string`: String de caracteres a ser desenhada.

## Como Usar

1. **Inicialização do Display**:
   Chame a função `ssd1306_init()` para inicializar o display e configurá-lo corretamente.

2. **Desenhar Caracteres**:
   Use `ssd1306_draw_char_absolute()` ou `ssd1306_draw_string_absolute()` para desenhar caracteres e strings no display.

3. **Desenha textos com quebra de linha**:
   Use `ssd1306_draw_string_with_word_wrap` para desenhar um texto na tela, onde o mesmo será quebrado automáticamente com base na largura do display.

5. **Atualizar a Tela**:
   Use `render_on_display()` para renderizar uma parte específica do display.

## Exemplo
```c
#include "pico/stdlib.h"
#include "hardware/i2c.h"
#include "ssd1306.h" // Inclua o arquivo de cabeçalho da biblioteca do SSD1306

// Definição da area de renderização
struct render_area frame_area = {
    start_column : 0,
    end_column : ssd1306_width - 1,
    start_page : 0,
    end_page : ssd1306_n_pages - 1
};

// Buffer de renderização do display
uint8_t ssd[ssd1306_buffer_length];

// Função para inicializar o display
void init_oled() {
    // Inicialização do I2C no Raspberry Pi Pico W
    i2c_init(i2c1, ssd1306_i2c_clock * 1000);
    gpio_set_function(4, GPIO_FUNC_I2C); // SDA
    gpio_set_function(5, GPIO_FUNC_I2C); // SCL
    gpio_pull_up(4); // Pull-up para SDA
    gpio_pull_up(5); // Pull-up para SCL

    // Inicialização do display SSD1306
    ssd1306_init(); // Função fornecida pela sua biblioteca

    // Preparar área de renderização para o display (ssd1306_width pixels por ssd1306_n_pages páginas)
    calculate_render_area_buffer_length(&frame_area);

    // Zera o buffer do display
    memset(ssd, 0, ssd1306_buffer_length);
}

// Função principal
int main() {
    // Inicializa o display OLED
    init_oled();

    // Exemplo: Exibindo uma string no display
    const char *message = "Olá, Mundo!";
    
    // Limpa o buffer do display
    memset(ssd, 0, ssd1306_buffer_length);

    // Desenha a string no display (posição inicial 5, 1)
    ssd1306_draw_string_absolute(ssd, 5, 1, message);

    // Atualiza o display com o conteúdo do buffer
    render_on_display(ssd, &frame_area);

    // Loop infinito (display exibirá a mensagem até ser desligado)
    while (1) {
        // Você pode adicionar outras lógicas aqui se necessário
        sleep_ms(1000); // Intervalo de 1 segundo
    }

    return 0;
}
```
