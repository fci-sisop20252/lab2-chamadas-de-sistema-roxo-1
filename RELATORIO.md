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
- Linhas: 24 (esperado: 25)
- Caracteres: 1299
- Chamadas read(): 21
- Tempo: 0.000304 segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |       87        | 0.000391  |
| 64          |       21        | 0.000304  |
| 256         |        6        | 0.000127  |
| 1024        |        2        | 0.000165  |

### Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quanto maior o tamanho do buffer, a syscall lê uma quantidade maior de dados do arquivo a cada vez, ou seja, menos syscall's.
```

**2. Como você detecta o fim do arquivo?**

```
Pois ao chegar ao final do arquivo e não ter mais bytes para ler, a função retornou 0.
```

---

## Exercício 4 - Cópia de Arquivo

### Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000157 segundos
- Throughput: 8484.28 KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Pois syscall não garante que todos os bytes serão escritos em uma única operação.
```

**2. Que flags são essenciais no open() do destino?**

```
O_WRONLY: Abre o arquivo apenas para escrita, garantindo que você não leia acidentalmente do arquivo de destino.

O_CREAT: Cria o arquivo se ele não existir, sem essa flag, o programa falharia se o arquivo destino.txt não existisse.

O_TRUNC: Trunca o arquivo para o tamanho zero se ele já existir, garante que o conteúdo do arquivo de origem não seja apenas adicionado ao final de um arquivo de destino já existente, mas sim o substitua completamente.
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Syscalls são a ponte entre o seu código e o sistema operacional, permitindo que seu programa, que roda em um ambiente protegido (espaço do usuário), acesse recursos e serviços privilegiados do kernel. Elas demonstram a transição entre os dois espaços, garantindo a segurança e estabilidade do sistema.```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
Os file descriptors são identificadores numéricos que o kernel usa para se referir a arquivos, dispositivos e outras fontes/destinos de entrada e saída. Eles simplificam a interação do seu programa com os recursos do sistema, permitindo que você use um número simples para operações como ler ou escrever, em vez de lidar diretamente com os nomes dos arquivos.```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Quanto maior o tamanho do buffer, a syscall lê uma quantidade maior de dados do arquivo a cada vez, ou seja, menos syscall's, mais rápido e eficiente.
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
O cp, pois é uma ferramenta de produção que foi otimizada para a velocidade máxima.```

---

## Entrega

Certifique-se de ter:
- [X] Todos os códigos com TODOs completados
- [X] Traces salvos em `traces/`
- [X] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```