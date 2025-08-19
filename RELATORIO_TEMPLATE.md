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

printf() é mais fácil de usar e é a escolha comum para a maioria das tarefas. (Função de uma biblioteca)
write() é mais poderosa e usada quando você precisa de controle total sobre a saída, especialmente em programação de sistemas. (chamada de sistema)


**3. Qual implementação você acha que é mais eficiente? Por quê?**

```
printf(), pois além de ser de alto nível, agrupa muitas operações em uma única syscall
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
O descritor de arquivo retornado pela chamada open() não foi 0, 1 ou 2 porque esses números são reservados para os descritores de arquivo padrão do sistema:

0: stdin (entrada padrão)

1: stdout (saída padrão)

2: stderr (saída de erro padrão)

Esses descritores são abertos automaticamente pelo sistema operacional quando um programa é iniciado. A função open() sempre atribui o próximo descritor de arquivo disponível, que, neste caso, seria o 3.
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Pois ao chegar ao final do arquivo e não ter mais bytes para ler, a função retornou 0.
```

---

## Exercício 3 - Contador com Loop

### Resultados (BUFFER_SIZE = 64):
- Linhas: _____ (esperado: 25)
- Caracteres: _____
- Chamadas read(): _____
- Tempo: _____ segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |                 |           |
| 64          |                 |           |
| 256         |                 |           |
| 1024        |                 |           |

### Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
[Sua análise aqui]
```

**2. Como você detecta o fim do arquivo?**

```
[Sua análise aqui]
```

---

## Exercício 4 - Cópia de Arquivo

### Resultados:
- Bytes copiados: _____
- Operações: _____
- Tempo: _____ segundos
- Throughput: _____ KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [ ] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
[Sua análise aqui]
```

**2. Que flags são essenciais no open() do destino?**

```
[Sua análise aqui]
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
[Sua análise aqui]
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
[Sua análise aqui]
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
[Sua análise aqui]
```

### Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** _____

**Por que você acha que foi mais rápido?**

```
[Sua análise aqui]
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