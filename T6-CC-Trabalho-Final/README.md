# PixelArtDSL - Compilador T6

Este projeto implementa a **PixelArtDSL**, uma Linguagem de Domínio Específico (DSL) declarativa focada na criação e renderização de pixel arts vetoriais de alta resolução via HTML/SVG. Foi desenvolvido como o Trabalho Final (T6) da disciplina de Compiladores (DC/UFSCar).

## Integrantes do Grupo

* Eduardo Eudoro Lemos de Oliveira - RA 824757
* Maylon Martins de Melo - RA 800199

## Demonstração (Pitch de Vendas)

O vídeo demonstrativo pode ser encontrado no link: `[INSIRA O LINK DO SEU VÍDEO NO YOUTUBE AQUI]`
---

## Estrutura da Linguagem

A linguagem é dividida em 3 blocos obrigatórios: `canvas`, `palette`, e `draw`.

```text
// 1. Definição da área de pintura e cor de fundo
canvas 32 x 32 background #1E1E1E

// 2. Definição dos nomes amigáveis para cores hexadecimais
palette {
    red = #FF5555
    blue = #8BE9FD
}

// 3. Comandos de Pintura
draw {
    pixel (10, 10) red
    rect (5, 5) to (15, 15) blue
    line (0, 0) to (31, 31) red
    circle center (16, 16) radius 5 blue
}
```

## Arquitetura do Compilador

O projeto preenche integralmente os critérios da disciplina:

### 1. Análise Léxica e Sintática (ALS)

A gramática `PixelArt.g4` define uma estrutura forte, garantindo que coordenadas possuam formatação estrita `(X, Y)` e cores sigam a notação `#XXXXXX`. Erros lexicais/sintáticos encerram o processo precocemente.

### 2. Análise Semântica (AS)

Realiza 4 verificações contextuais cruciais:

1. **Uso de Cores Indefinidas**: Acusa erro se usar uma cor inexistente no bloco `palette`.
2. **Cores Redefinidas**: Evita sombreamento/redclaração de nomes na paleta.
3. **Out-of-Bounds Categórico**: Verifica se as coordenadas inseridas estão fisicamente dentro da largura e altura demarcada no cabeçalho `canvas`.
4. **Violações Geométricas**: Verifica se um raio de círculo excede os limites de borda, calculando fisicamente `x - r < 0` em tempo de compilação.

### 3. Geração de Código ou Interpretação (GCI)

Todo programa válido gera um arquivo de saída **HTML autônomo**. O `CodeGeneratorVisitor` mapeia a árvore abstrata em nós padrão W3C `<svg>` mantendo a propriedade CSS `shape-rendering: crispEdges;`, convertendo lógicas vetorizadas simples em artes perfeitamente redimensionáveis.

---

## Instalação e Execução

### Dependências

- Python 3
- ANTLR4 Runtime (`pip install antlr4-python3-runtime==4.13.2`)
- Java JRE (Opcional: Apenas para recompilar a gramática se alterada).

### Compilar e Rodar o Gerador

Para rodar a versão atual (já compilada do arquivo `.g4` para python):

```bash
# Executar a compilação de um arquivo específico
python main.py <arquivo_entrada.pxl> <arquivo_saida.html>

# Exemplo de teste incluso:
python main.py casos_de_teste/1_valido.pxl teste_valido.html
```

### Rodar os Casos de Teste Oficiais

Criamos um script que varre casos positivos e negativos exibindo os relatórios de erros no terminal e gerando a saída quando válida:

```bash
# Windows
test.bat
```

### Para Re-gerar o Parser (Modificação da Gramática)

Caso deseje alterar o `PixelArt.g4`, rode:

```bash
java -jar antlr-4.13.2-complete.jar -Dlanguage=Python3 -visitor PixelArt.g4
```
