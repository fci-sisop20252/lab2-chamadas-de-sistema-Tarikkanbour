# Relatório do Laboratório 2 - Chamadas de Sistema

---

## Exercício 1a - Observação printf() vs 1b - write()

### Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre printf() e write()?**

```
Pois eles trabalham em níveis diferentes, o write em um nível mais baixo, tendo um custo maior, já o printf em um nível mais alto, sendo menos custoso.
```

**3. Qual implementação você acha que é mais eficiente? Por quê?**

```
Printf é mais eficiente, já que ele economiza chamadas de sistema se comparado com o write.
```

---

## Exercício 2 - Leitura de Arquivo

### Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### Comando strace:
```bash
strace -e open,read,close ./ex2_leitura
```

### Análise

**1. Por que o file descriptor não foi 0, 1 ou 2?**

```
Pois 0, 1 e 2 já estavam ocupados desde o começo do programa.
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Pois a função open() retorna o menor número não negativo que serve para mostrar o arquivo aberto.
```

---

## Exercício 3 - Contador com Loop

### Resultados (BUFFER_SIZE = 64):
- Linhas: 24 (esperado: 25)
- Caracteres: 1299
- Chamadas read(): 21
- Tempo: 0.000625 segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |        82       | 0.000202  |
| 64          |        21       | 0.000090  |
| 256         |        6        | 0.000066  |
| 1024        |        2        | 0.000058  |

### Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quando o buffer aumenta, as chamadas de sistemas diminuem.
```

**2. Como você detecta o fim do arquivo?**

```
A partir do read() que retorna o valor 0 ("exited with 0"), indicando que chegou ao fim do arquivo.
```

---

## Exercício 4 - Cópia de Arquivo

### Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000189 segundos
- Throughput: 7047.78 KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Pois, dessa forma, pode-se confirmar se todo o arquivo foi escrito e lido corretamente.
```

**2. Que flags são essenciais no open() do destino?**

```
O_TRUNC, O_CREAT e O_WRONLY.
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Elas demonstram a partir do strace que retorna o número de bytes.
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
Eles representam um mecanismos fundamentais, como referências numéricas para recursos de E/S, canais de comunicação entre o processo e o sistema operacional e identificadores únicos.
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
O tamanho do buffer interfere diretamente na performance, já que, quanto maior o buffer, melhor o desempenho, assim, fazendo menos chamadas de sistema.
```

### Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** meu programa

**Por que você acha que foi mais rápido?**

```
Provavelmente pelo fato do meu programa fazer menos chamadas de sistema.
```

---

## Entrega

Certifique-se de ter:
- [ ] Todos os códigos com TODOs completados
- [ ] Traces salvos em `traces/`
- [ ] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```