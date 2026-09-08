#include <iostream>

#define MAX 50

typedef int TIPOCHAVE;

typedef struct
{
    TIPOCHAVE chave;
} REGISTRO;

typedef struct
{
    REGISTRO A[MAX + 1];
    int nroElem;
} LISTA;

void inicializarLista(LISTA *l)
{
    l->nroElem = 0;
}

// ====================================================================
// FUNÇÃO BASE DE INSERÇÃO (Deslocamento de elementos)
// ====================================================================
// Inserção "direta" em uma posição 'i' específica
bool inserirElemListaSeq(TIPOCHAVE ch, int i, LISTA *l)
{
    int j;
    if ((l->nroElem >= MAX) || (i < 0) || (i > l->nroElem))
        return false; // lista cheia ou índice inválido
    // Deslocamento dos elementos para a direita para abrir espaço
    if ((l->nroElem > 0) && (i < l->nroElem))
    {
        for (j = l->nroElem; j >= (i + 1); j--)
        {
            l->A[j] = l->A[j - 1];
        }
    }
    l->A[i].chave = ch;
    l->nroElem++;
    return true;
}

// 1. Inserir no final
bool inserirNoFinal(TIPOCHAVE ch, LISTA *l)
{
    // Insere na posição exata correspondente ao 'nroElem' (final)
    return inserirElemListaSeq(ch, l->nroElem, l);
}

// 2. Inserir no início
bool inserirNoInicio(TIPOCHAVE ch, LISTA *l)
{
    // Insere na posição 0, forçando o deslocamento de todos à direita
    return inserirElemListaSeq(ch, 0, l);
}

// 3. Inserir ordenado (sem duplicação)
bool inserirElemListaOrd(TIPOCHAVE ch, LISTA *l)
{
    int i = 0;
    if (l->nroElem >= MAX)
        return false;            // lista cheia
    l->A[l->nroElem].chave = ch; // uso de sentinela no final
    while (l->A[i].chave < ch)
        i++;
    // Evita duplicação do elemento
    if ((l->A[i].chave == ch) && (i < l->nroElem))
        return false;
    else
        return inserirElemListaSeq(ch, i, l);
}

// ====================================================================
// OPERAÇÕES DE REMOÇÃO E BUSCA
// ====================================================================
// Busca sequencial padrão em lista não ordenada
int buscaSeq(TIPOCHAVE ch, LISTA l)
{
    int i = 0;
    while (i < l.nroElem)
    {
        if (ch == l.A[i].chave)
            return i; // achou
        else
            i++;
    }
    return -1; // não achou
}

// Remover um elemento por meio de deslocamento sobreposto
bool excluirElemLista(TIPOCHAVE ch, LISTA *l)
{
    int pos, j;
    pos = buscaSeq(ch, *l);
    if (pos == -1)
        return false; // não existe
    // Puxa todos os elementos uma posição para a esquerda
    for (j = pos; j < l->nroElem - 1; j++)
    {
        l->A[j] = l->A[j + 1];
    }
    l->nroElem--;
    return true;
}

// Busca com Sentinela (Variação para listas Desordenadas)
// A sentinela garante que o laço pare sem precisar testar os limites do vetor
int buscaSentinela(TIPOCHAVE ch, LISTA l)
{
    int i = 0;
    l.A[l.nroElem].chave = ch; // Atribui a chave no final (sentinela)
    while (l.A[i].chave != ch)
        i++; // Otimiza reduzindo as comparações
    if (i == l.nroElem)
        return -1; // Só achou na sentinela (não existia)
    else
        return i; // Achou em uma posição válida
}

// Busca Binária (Requer que a lista esteja obrigatoriamente ORDENADA)
int buscaBin(TIPOCHAVE ch, LISTA l)
{
    int inf, sup, meio;
    inf = 0;
    sup = l.nroElem - 1;
    while (inf <= sup)
    {
        meio = ((inf + sup) / 2);
        if (l.A[meio].chave == ch)
            return meio; // achou
        else
        {
            if (l.A[meio].chave < ch)
                inf = meio + 1; // busca na metade superior
            else
                sup = meio - 1; // busca na metade inferior
        }
    }
    return -1; // não achou
}

// Função auxiliar para imprimir os elementos da lista
void imprimirLista(LISTA l)
{
    std::cout << "[ ";
    for (int i = 0; i < l.nroElem; i++)
    {
        std::cout << l.A[i].chave << " ";
    }
    std::cout << "]\n"
              << std::endl;
}

// Função para exibir o menu de operações
int menu(){
    int opcao;
    std::cout << "Menu de Operações:\n";
    std::cout << "1. Inserir no final\n";
    std::cout << "2. Inserir no início\n";
    std::cout << "3. Inserir ordenado\n";
    std::cout << "4. Buscar elemento\n";
    std::cout << "5. Excluir elemento\n";
    std::cout << "6. Imprimir lista\n";
    std::cout << "0. Sair\n";
    std::cout << "Escolha uma opção: ";
    std::cin >> opcao;
    return opcao;
}

int main()
{
    LISTA lista;
    inicializarLista(&lista);

    int opcao;
    TIPOCHAVE chave;

    do {
        opcao = menu();
        switch (opcao) {
            case 1:
                std::cout << "Digite a chave para inserir no final: ";
                std::cin >> chave;
                if (inserirNoFinal(chave, &lista)) {
                    std::cout << "Elemento inserido no final.\n";
                } else {
                    std::cout << "Falha ao inserir. Lista cheia.\n";
                }
                break;
            case 2:
                std::cout << "Digite a chave para inserir no início: ";
                std::cin >> chave;
                if (inserirNoInicio(chave, &lista)) {
                    std::cout << "Elemento inserido no início.\n";
                } else {
                    std::cout << "Falha ao inserir. Lista cheia.\n";
                }
                break;
            case 3:
                std::cout << "Digite a chave para inserir ordenado: ";
                std::cin >> chave;
                if (inserirElemListaOrd(chave, &lista)) {
                    std::cout << "Elemento inserido ordenadamente.\n";
                } else {
                    std::cout << "Falha ao inserir. Lista cheia ou elemento duplicado.\n";
                }
                break;
            case 4:
                std::cout << "Digite a chave para buscar: ";
                std::cin >> chave;
                int pos;
                pos = buscaSeq(chave, lista);
                if (pos != -1) {
                    std::cout << "Elemento encontrado na posição: " << pos << "\n";
                } else {
                    std::cout << "Elemento não encontrado.\n";
                }
                break;
            case 5:
                std::cout << "Digite a chave para excluir: ";
                std::cin >> chave;
                if (excluirElemLista(chave, &lista)) {
                    std::cout << "Elemento excluído.\n";
                } else {
                    std::cout << "Falha ao excluir. Elemento não encontrado.\n";
                }
                break;
            case 6:
                imprimirLista(lista);
                break;
            case 0:
                std::cout << "Saindo...\n";
                break;
            default:
                std::cout << "Opção inválida. Tente novamente.\n";
        }
    } while (opcao != 0);

    return 0;
}

