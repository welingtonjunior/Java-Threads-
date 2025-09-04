# Guia de Estudos: Java Threads

Este repositório tem como objetivo ser um **guia de estudos** sobre
Threads em Java.\
Aqui você encontrará conceitos, exemplos e boas práticas para trabalhar
com **concorrência e paralelismo**.

------------------------------------------------------------------------

## 📌 1. O que são Threads?

-   Uma **thread** é a menor unidade de execução de um processo.
-   No Java, uma aplicação inicia com uma **thread principal** chamada
    `main`.
-   Criar threads permite executar tarefas de forma **concorrente**
    (quase ao mesmo tempo).

------------------------------------------------------------------------

## 📌 2. Maneiras de Criar Threads

### 2.1 Estendendo `Thread`

``` java
class MinhaThread extends Thread {
    public void run() {
        System.out.println("Executando na thread: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        MinhaThread t1 = new MinhaThread();
        t1.start();
    }
}
```

### 2.2 Implementando `Runnable`

``` java
class MinhaRunnable implements Runnable {
    public void run() {
        System.out.println("Rodando com Runnable");
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MinhaRunnable());
        t1.start();
    }
}
```

### 2.3 Usando **Lambda**

``` java
public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> System.out.println("Thread com Lambda"));
        t1.start();
    }
}
```

------------------------------------------------------------------------

## 📌 3. Métodos Importantes

-   `start()` → inicia a execução da thread.
-   `run()` → contém o código que será executado.
-   `sleep(ms)` → pausa a thread.
-   `join()` → espera outra thread terminar.
-   `interrupt()` → envia sinal para parar a execução.

------------------------------------------------------------------------

## 📌 4. Problemas Comuns

-   **Race Condition**: duas threads acessam recurso ao mesmo tempo →
    resultados imprevisíveis.
-   **Deadlock**: duas ou mais threads ficam esperando recursos que
    nunca são liberados.
-   **Starvation**: uma thread nunca ganha tempo de CPU.

------------------------------------------------------------------------

## 📌 5. Sincronização

Usar `synchronized` para evitar acesso simultâneo a recursos críticos.

``` java
class Contador {
    private int valor = 0;

    public synchronized void incrementar() {
        valor++;
    }

    public int getValor() {
        return valor;
    }
}
```

------------------------------------------------------------------------

## 📌 6. Comunicação Entre Threads

-   `wait()` → pausa a thread até ser notificada.
-   `notify()` → acorda uma thread em espera.
-   `notifyAll()` → acorda todas as threads em espera.

Exemplo:

``` java
class Mensagem {
    private String texto;
    private boolean disponivel = false;

    public synchronized void escrever(String msg) {
        while (disponivel) {
            try { wait(); } catch (InterruptedException e) { }
        }
        texto = msg;
        disponivel = true;
        notify();
    }

    public synchronized String ler() {
        while (!disponivel) {
            try { wait(); } catch (InterruptedException e) { }
        }
        disponivel = false;
        notify();
        return texto;
    }
}
```

------------------------------------------------------------------------

## 📌 7. Executor Framework (Melhor Prática)

Em vez de criar threads manualmente, use `ExecutorService`.

``` java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(2);

        executor.submit(() -> System.out.println("Tarefa 1"));
        executor.submit(() -> System.out.println("Tarefa 2"));

        executor.shutdown();
    }
}
```

------------------------------------------------------------------------

## 📌 8. Classes Importantes do `java.util.concurrent`

-   `ExecutorService` → gerenciamento de threads.
-   `Future` → representa resultado de uma tarefa assíncrona.
-   `Callable` → similar a `Runnable`, mas retorna valor.
-   `CountDownLatch` → espera múltiplas threads terminarem.
-   `Semaphore` → controla número de acessos a um recurso.
-   `ConcurrentHashMap` → mapa seguro para múltiplas threads.

------------------------------------------------------------------------

## 📌 9. Boas Práticas

✅ Prefira **ExecutorService** a criar threads diretamente.\
✅ Sempre trate exceções dentro das threads.\
✅ Evite **variáveis compartilhadas** sem sincronização.\
✅ Teste com cenários concorrentes para identificar **race conditions**.

------------------------------------------------------------------------

## 📌 10. Recursos para Estudo

-   📖 *Java Concurrency in Practice* --- Brian Goetz\
-   📖 *Effective Java* --- Joshua Bloch (capítulo de threads e
    concorrência)\
-   📚 Documentação oficial: [Oracle
    Concurrency](https://docs.oracle.com/javase/tutorial/essential/concurrency/)

------------------------------------------------------------------------

## 📌 11. Concorrência vs Paralelismo

### Concorrência

-   Lidar com múltiplas tarefas ao mesmo tempo, mas **não
    necessariamente em paralelo**.\
-   Uma CPU de um único núcleo pode alternar entre tarefas, dando a
    impressão de simultaneidade.\
-   Threads em Java são uma forma de implementar **concorrência**.

### Paralelismo

-   Execução real de várias tarefas ao mesmo tempo, usando **múltiplos
    núcleos de CPU**.\
-   Requer suporte de hardware (processadores multi-core).\
-   Java oferece **Fork/Join Framework** e **Parallel Streams** para
    paralelismo.

### Exemplo com Parallel Streams

``` java
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        Arrays.asList(1, 2, 3, 4, 5)
              .parallelStream()
              .forEach(n -> System.out.println(n + " - " + Thread.currentThread().getName()));
    }
}
```

------------------------------------------------------------------------
