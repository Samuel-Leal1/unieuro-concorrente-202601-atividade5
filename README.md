# RELATÓRIO DE AVALIAÇÃO DE PERFORMANCE

## Comparação de Similaridade de Perguntas com MPI
**Dataset:** Quora Question Pairs  

**Disciplina:** Computação Paralela e Distribuída  
**Aluno:** Samuel Leal de Araujo 
**Professor:** Rafael Marconi Ramos  
**Data:** 20/03/2026  

---

## 1. Descrição do Problema

O programa implementa um sistema de detecção de similaridade entre perguntas extraídas do dataset Quora Question Pairs, utilizando o algoritmo de Similaridade de Jaccard. O objetivo é identificar, entre um conjunto de perguntas, quais pares possuem maior grau de semelhança semântica baseada em sobreposição de tokens.

### Objetivo
Calcular a similaridade entre todos os pares possíveis de perguntas de um dataset real (Quora), identificando os pares mais similares. A versão paralela com MPI visa reduzir o tempo de execução distribuindo o processamento entre múltiplos processos.

### Volume de dados
- 5.000 perguntas (coluna `question1`)
- Total de comparações: **12.497.500**

### Algoritmo
**Similaridade de Jaccard:**


- Varia de 0 a 1

### Complexidade
- Complexidade: **O(n²)**
- Justifica o uso de paralelização

---

## 2. Ambiente Experimental

| Item | Descrição |
|------|----------|
| Processador | Intel Core i5-12500 |
| Núcleos | 12 (6 P-cores + 4 E-cores) |
| RAM | 16 GB |
| Sistema Operacional | Windows 11 |
| Linguagem | Python 3.14 |
| Biblioteca | mpi4py |
| MPI | MS-MPI |

---

## 3. Metodologia de Testes

### Medição de tempo
- Função `time.time()`
- Mede apenas processamento + comunicação MPI
- Não inclui leitura do CSV

### Configurações testadas
- 1 processo
- 2 processos
- 4 processos
- 8 processos
- 12 processos

### Procedimento
- Cada teste executado **1 vez**
- Execução sequencial
- Ambiente controlado

### Comando
mpiexec -n <N> python avaliadormpi.py

## 4. Resultados Experimentais

Os tempos de execução obtidos para cada configuração foram:

- 1 processo: 40.47 segundos  
- 2 processos: 30.27 segundos  
- 4 processos: 20.24 segundos  
- 8 processos: 14.78 segundos  
- 12 processos: 12.84 segundos  

---

## 5. Cálculo de Speedup e Eficiência

O speedup foi calculado utilizando a fórmula:

Speedup(p) = T(1) / T(p)

Onde T(1) representa o tempo com 1 processo e T(p) o tempo com p processos.

A eficiência foi calculada como:

Eficiência(p) = Speedup(p) / p

A eficiência indica o aproveitamento dos processos em relação ao desempenho ideal.

---

## 6. Resultados de Speedup e Eficiência

Com base nos tempos medidos, os valores obtidos foram:

- 1 processo: speedup 1.00, eficiência 1.000  
- 2 processos: speedup 1.34, eficiência 0.670  
- 4 processos: speedup 2.00, eficiência 0.500  
- 8 processos: speedup 2.74, eficiência 0.343  
- 12 processos: speedup 3.15, eficiência 0.263  

O speedup ideal para 12 processos seria 12.00, porém o valor obtido foi 3.15, representando aproximadamente 26,3% do ideal.

---

## 7. Gráfico de Tempo de Execução

Observa-se uma redução significativa no tempo de execução conforme o número de processos aumenta, principalmente entre 1 e 4 processos.

A partir de 8 processos, os ganhos tornam-se menores, indicando que o custo de comunicação começa a impactar o desempenho.

---

## 8. Gráfico de Speedup

O speedup cresce de forma sub-linear em relação ao ideal.

O melhor resultado proporcional foi obtido com 4 processos, com speedup de 2.00.

Após esse ponto, a diferença entre o desempenho real e o ideal aumenta consideravelmente.

---

## 9. Gráfico de Eficiência

A eficiência diminui conforme o número de processos aumenta.

O valor passa de 1.00 com 1 processo para 0.26 com 12 processos.

Esse comportamento é esperado em aplicações paralelas com comunicação intensiva.

---

## 10. Análise dos Resultados

O speedup obtido não foi próximo do ideal. O valor máximo alcançado foi de 3.15 com 12 processos, enquanto o ideal seria 12.

A aplicação apresenta escalabilidade limitada. Embora o tempo diminua com o aumento de processos, essa redução ocorre de forma cada vez menor.

A eficiência começa a cair já a partir de 2 processos, com queda mais acentuada entre 4 e 8 processos.

O número de processos utilizado não ultrapassa o número de núcleos disponíveis, evitando sobrecarga por compartilhamento de CPU.

O principal overhead identificado está nas operações de comunicação, especialmente no uso de gather, que concentra todos os resultados no processo raiz.

Outros fatores que impactam negativamente o desempenho incluem:
- Parte sequencial do programa (Lei de Amdahl)
- Alto custo de serialização de dados
- Desbalanceamento de carga entre processos
- Overhead de inicialização e sincronização do MPI

---

## 11. Conclusão

A paralelização com MPI resultou em melhoria significativa no desempenho, reduzindo o tempo de execução de 40.47 segundos para 12.84 segundos.

O melhor equilíbrio entre speedup e eficiência foi obtido com 4 processos, atingindo speedup de 2.00 e eficiência de 50%.

A aplicação não escala de forma eficiente para um número maior de processos devido ao alto custo de comunicação e à presença de partes sequenciais no algoritmo.

---

## Melhorias Sugeridas

- Reduzir o volume de dados enviados no gather, utilizando apenas os melhores resultados (top-K)
- Melhorar o balanceamento de carga entre processos
- Utilizar bibliotecas como NumPy para otimização do processamento
- Aumentar o tamanho do dataset para melhor aproveitamento da paralelização
