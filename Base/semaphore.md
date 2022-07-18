# semaphore
Для решение проблемы race condition Дейкстра придумал семафор - переменная которая равна либо нулю, либо какому-нибудь положительному числу. С помощью него мы устанавливаем какое количество потоков могут использовать объект, в отличии от [[mutex и lock_guard]], семафор сам освобождает потоки. Для изменения семафора используются [[atomic|атомарные операции]] - т.е. если один поток начал изменение семафора, другой поток не сможет его изменить, даже если пришло его процессорное время.