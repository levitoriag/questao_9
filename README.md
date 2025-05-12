#include <stdio.h>
#include <stdlib.h>

// Função para ler um número positivo com validação
int lerEntradaPositiva(const char *mensagem) {
    int valor;
    while (1) {
        printf("%s", mensagem);
        if (scanf("%d", &valor) == 1 && valor > 0) {
            return valor;
        } else {
            printf("Entrada inválida. Digite um número inteiro positivo.\n");
            while (getchar() != '\n');
        }
    }
}

// Função para calcular o MDC com detalhes
int calcularMDC(int a, int b) {
    printf("MDC(%d, %d) = ", a, b);
    while (b != 0) {
        int temporario = b;
        b = a % b;
        a = temporario;
        printf("MDC(%d, %d) -> ", a, b);
    }
    printf("%d\n", a);
    return a;
}

// Função para calcular o inverso modular com passo a passo



int calcularInversoModular(int a, int m) {
    int oldR = a, r = m;
    int oldS = 1, s = 0;
    int oldT = 0, t = 1;
    int passo = 1;

printf("\nPasso 2: Inverso de G em Z(%d)\n", m);
    while (r != 0) {
        int q = oldR / r;
        int temporario;

        temporario = r;
        r = oldR - q * r;
        oldR = temporario;

        temporario = s;
        s = oldS - q * s;
        oldS = temporario;

        temporario = t;
        t = oldT - q * t;
        oldT = temporario;

        printf("Passo %d: q = %d, r = %d, oldR = %d, oldS = %d, S = %d\n",
               passo++, q, r, oldR, oldS, s);
    }

    if (oldR > 1) {
        printf("Erro: Não existe inverso modular para %d em Z(%d)\n", a, m);
        exit(1);
    }

    if (oldS < 0) oldS += m;
    printf("Inverso: %d\n", oldS);
    return oldS;
    }
    // Verifica se é primo 
    int verificarNumeroPrimo(int n) {
    if (n <= 1) return 0;
    if (n <= 3) return 1;
    if (n % 2 == 0 || n % 3 == 0) return 0;
    for (int i = 5; i * i <= n; i += 6)
        if (n % i == 0 || n % (i + 2) == 0) return 0;
    return 1;
    }
    
    // Função totiente de Euler
    int calcularEuler(int n) {
    int r = n;
    int i = 2;
    if (n % i == 0) {
        while (n % i == 0) n /= i;
        r -= r / i;
    }
    for (i = 3; i * i <= n; i += 2) {
        if (n % i == 0) {
            while (n % i == 0) n /= i;
            r -= r / i;
        }
    }
    if (n > 1) r -= r / n;
    return r;
    }
    
    // Exponenciação modular rápida
    int potenciaModular(int base, int exp, int mod) {
    int result = 1;
    base %= mod;
    while (exp > 0) {
        if (exp % 2 == 1) {
            result = ((long long)result * base) % mod;
        }
        base = ((long long)base * base) % mod;
        exp /= 2;
    }
    return result;
    }
    
    int main() {
    printf("----- Forneça os Valores -----\n");
    int valorH = lerEntradaPositiva("H: ");
    int valorG = lerEntradaPositiva("G: ");
    int modN = lerEntradaPositiva("n: ");
    int exponenteX = lerEntradaPositiva("x: ");
    int modN1 = lerEntradaPositiva("n1: ");
    printf("------------------------------\n");

    // Passo 1: Verificar se G e N são coprimos
    printf("\nPasso 1: Verificação de G e N\n");
    int mdcVal = calcularMDC(valorG, modN);
    if (mdcVal != 1) {
        printf("Erro: %d e %d não são coprimos. A divisão modular não é possível.\n", valorG, modN);
        return 1;
    } else {
        printf("OK: %d e %d são coprimos.\n", valorG, modN);
    }

    // Passo 2: Encontrar inverso modular de G
    int inversoG = calcularInversoModular(valorG, modN);

    // Passo 3: Calcular a = H / G mod n
    printf("\nPasso 3: Divisão H por G em Z(%d)\n", modN);
    int valorA = (valorH * inversoG) % modN;
    printf("a = (%d * %d) mod %d = %d\n", valorH, inversoG, modN, valorA);

    // Passo 4: Verificar se a e n1 são coprimos
    printf("\nPasso 4: Coprimalidade de A e N1\n");
    int mdcA_N1 = calcularMDC(valorA, modN1);
    if (mdcA_N1 != 1) {
        printf("Erro: %d e %d não são coprimos. Teoremas de Fermat e Euler não se aplicam.\n", valorA, modN1);
        return 1;
    } else {
        printf("OK: %d e %d são coprimos.\n", valorA, modN1);
    }

    // Passo 5: Verificar se n1 é primo
    printf("\nPasso 5: Primalidade de N1\n");
    int ehPrimo = verificarNumeroPrimo(modN1);
    if (ehPrimo) {
        printf("%d é primo\n", modN1);
    } else {
        printf("%d não é primo\n", modN1);
    }

    // Passo 6 e 7: Definir x1 com Fermat ou Euler
    int x1;
    if (ehPrimo) {
        printf("\nPasso 6: Pequeno Teorema de Fermat\n");
        x1 = modN1 - 1;
        printf("x1 = %d\n", x1);
    } else {
        printf("\nPasso 7: Teorema de Euler\n");
        x1 = calcularEuler(modN1);
        printf("φ(%d) = %d\n", modN1, x1);
    }

    // Passo 8: Decompor X
    printf("\nPasso 8: Decomposição de X\n");
    int q = exponenteX / x1;
    int r = exponenteX % x1;
    printf("%d = %d * %d + %d\n", exponenteX, x1, q, r);

    // Passo 9: Reescrita da expressão
    printf("\nPasso 9: Reescritura da expressão\n");
    printf("a^x ≡ (((a^x1)^q mod n1) * (a^r mod n1)) mod n1\n");

    // Passo 10: Calcular potências intermediárias
    printf("\nPasso 10: Cálculos intermediários\n");
    int a_x1 = potenciaModular(valorA, x1, modN1);
    printf("a^x1 mod n1 = %d\n", a_x1);

    int a_x1_q = potenciaModular(a_x1, q, modN1);
    printf("(a^x1)^q mod n1 = %d\n", a_x1_q);

    int a_r = potenciaModular(valorA, r, modN1);
    printf("a^r mod n1 = %d\n", a_r);

    // Passo 11: Resultado final
    printf("\nPasso 11: Resultado final\n");
    int resultado = ((long long)a_x1_q * a_r) % modN1;
    printf("((a^x1)^q * a^r) mod n1 = %d\n", resultado);
    printf("%d^%d mod %d = %d n", valorA, exponenteX, modN1, resultado);

    return 0;
}
