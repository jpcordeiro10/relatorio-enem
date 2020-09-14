#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <locale.h>
#include <conio.h>
#include <time.h>
#include <ctype.h>

#define TAM 100

typedef struct data DATA;
struct data{
    int dia;
    int mes;
    int ano;
};

typedef struct aniversario ANIVERSARIO; //STRUCT AUXILIAR USADA NO RELATÓRIO
struct aniversario{
    DATA birthday;
};

typedef struct pessoa PESSOA;
struct pessoa{
    char nome[TAM];
    char sexo[TAM];
    char curso_desejado[TAM];
    char unid_fed[TAM];
    DATA aniv;
};

// PROTOTIPO DE ALGUMAS FUNÇÕES
void cabecalho();
void tranformar_minuscula(char string[]);
void cadastrar_estado();
int procurar_estado(char busca[TAM]);
void cadastrar_curso();
int procurar_curso(char busca[]);
void cadastrar_pessoa();
void consultar_pessoa();
void listar_pessoas_estado();
void listar_pessoas_curso();
void relatorio_demografico();
int valida_data(int dia, int mes, int ano);
void remover_pessoas();


int main(void){
    int opcao;

    setlocale(LC_ALL, "Portuguese"); //Fazer com que o code blocks reconheça pontuação e portugues

    do{
        cabecalho();
        printf("1) Cadastrar Estado\n");
        printf("2) Cadastrar curso\n");
        printf("3) Cadastrar pessoa\n");
        printf("4) Listar pessoas por Estado\n");
        printf("5) Listar pessoas por curso desejado\n");
        printf("6) Consultar pessoa por nome\n");
        printf("7) Remover um candidato\n");
        printf("8) Gerar relatório demográfico\n");
        printf("9) Finalizar programa\n\n");

        printf("Selecione a opção desejada: ");
        scanf("%d", &opcao);

        switch(opcao){
        case 1:
            cadastrar_estado();
        break;

        case 2:
            cadastrar_curso();
        break;

        case 3:
            cadastrar_pessoa();
        break;

        case 4:
            listar_pessoas_estado();
        break;

        case 5:
            listar_pessoas_curso();
        break;

        case 6:
            consultar_pessoa();
        break;

        case 7:
            remover_pessoas();
        break;

        case 8:
            relatorio_demografico();
        break;

        case 9:
            printf("Programa encerrado, obrigado por sua visita!\n");
            getchar();
        break;

        default:
            printf("\n");
            printf("Opção inválida, selecione uma dentre as opções oferecidas\n");
            getchar();
            printf("Pressione enter para continuar\n");
            getchar();
        break;
        }
    } while(opcao != 9);


    return 0;
}

void cabecalho(){ //CABEÇALHO PADRÃO ENTRE TODAS AS FUNÇÕES
    system("cls");
    printf("---------------------------------\n");
    printf("---------RELATÓRIO ENEM----------\n");
    printf("----------------------------------\n\n");

}

void tranformar_minuscula(char string[]){ // RECEBE STRINGS DURANTE TODO O TRABALHO PARA TRANSFORMÁ-LAS EM MINÚSCULAS
    int tamanho, i;
    tamanho = strlen(string);

    for(i = 0; i < tamanho; i++){
        string[i] = tolower(string[i]);
    }

}


void cadastrar_estado(){ // FUNCAO PARA CADASTRAR O ESTADO NÃO PERMITINDO ESTADOS REPITIDOS
    FILE* Estado;
    Estado = fopen("Estado.txt", "ab");
    char estado[TAM];
    int cadastrado = 0; //VARIAVEL BOOLEANA PARA AVALIAR SE O ESTADO JÁ FOI CADASTRADO


    if(Estado == NULL){
        printf("Problemas ao abrir o arquivo! \n");
    } else {

            cabecalho();
            fflush(stdin);
            printf("Digite o Estado que deseja cadastrar: ");
            gets(estado);

            tranformar_minuscula(estado);

            cadastrado = procurar_estado(estado); // FUNCAO QUE VERIFICA SE O ESTADO JÁ FOI CADASTRADO


            if(cadastrado == 0){
                fwrite(&estado, sizeof(char), 100, Estado); // ESCREVE UMA STRING DO ESTADO DE ATE 100 CARACTERES
                printf("\nEstado cadastrado com sucesso!\n");
                printf("Pressione enter para continuar . . .\n");
                getchar();
            } else{
                printf("\nEstado já cadastrado\n");
                printf("Pressione enter para continuar . . .\n");
                getchar();
            }


        fclose(Estado);
    }
}
//FUNCAO QUE RECEBE UMA STRING ESTADO E RETORNA 0 SE ELE AINDA NÃO FOI CADASTRADO
// E RETORNA 1 SE ELE JÁ FOI CADASTRADO
int procurar_estado(char busca[]){
    int igual = 0;
    char estado[TAM];

    FILE* Estado;
    Estado = fopen("Estado.txt", "rb");

    if(Estado == NULL){
        printf("Problemas ao abrir o arquivo! \n");
        return 0;
    } else{
        while(fread(&estado, sizeof(char)*50, 1, Estado) == 1){ //Compotamento anomalo na função sizeof se colocar 100 por exemplo buga
            if(strcmp(busca, estado) == 0){
                igual = 1;

                break;
            }
        }
        return igual;
        fclose(Estado);
    }
}

void cadastrar_curso(){ // FUNCAO PARA CADASTRAR O CURSO NÃO PERMITINDO ESTADOS REPITIDOS
    FILE* Curso;
    char curso[100];
    int cadastrado = 0; // VARIAVEL BOOLEANA PARA VERIFICAR SE O CURSO JÁ FOI CADASTRADO OU NAO

    Curso = fopen("Curso.txt", "ab");

    if(Curso == NULL){
        printf("Problemas ao abrir o arquivo! \n");
    } else{
            cabecalho();
            fflush(stdin);
            printf("Digite o curso que deseja cadastrar: ");
            gets(curso);
            tranformar_minuscula(curso);

            cadastrado = procurar_curso(curso); //FUNCAO PARA VERIFICAR SE O CURSO FOI CADASTRADO

            if(cadastrado == 0){
                fwrite(&curso, sizeof(char), 100, Curso);
                printf("\nCurso cadastrado com sucesso!\n");
                printf("Pressione enter para continuar . . .\n");
                getchar();
            } else{
                printf("\nCurso já cadastrado!\n");
                printf("Pressione enter para continuar . . .\n");
                getchar();
            }
        fclose(Curso);
    }
}
//FUNCAO QUE RECEBE UMA STRING CURSO E RETORNA 0 SE ELE AINDA NÃO FOI CADASTRADO
// E RETORNA 1 SE ELE JÁ FOI CADASTRADO
int procurar_curso(char busca[]){
    char curso_procurado[TAM];
    int igual = 0;

    FILE* Curso;
    Curso = fopen("Curso.txt", "rb");

    if(Curso == NULL){
        printf("Problemas ao abrir o arquivo\n");
        return 0;
    } else{
        while(fread(&curso_procurado, sizeof(char)*50, 1, Curso) == 1){
            if(strcmp(curso_procurado, busca) == 0){
                igual = 1;
                break;
            }
        }
        return igual;
        fclose(Curso);
    }
}
//FUNCAO QUE CADASTRA OS DADOS DO CANDIDATO EM UMA STRUCT NAO PERMITINDO O CADASTRAMENTO DE DADOS INVÁLIDOS
//COMO UMA DATA INVÁLIDA, SEXO INVÁLIDO E VERIFICA SE A PESSOA ESTA SENDO CADASTRADA EM UM CURSO OU
//ESTADO QUE REALMENTE EXISTEM
void cadastrar_pessoa(){
    FILE* Pessoa;
    PESSOA gnt; //GNT É A ABREVIAÇÃO DE GENTE
    int achei_estado = 0; //VERIFICAR SE O ESTADO QUE ESTAO CADASTRANDO A PESSOA REALMENTE EXISTE
    int achei_curso = 0; //VERIFICAR SE O CURSO QUE ESTAO CADASTRANDO A PESSOA REALMENTE EXISTE
    int validez_data;
    int validar_masculino;
    int validar_feminino;
    int validar_sexo;
    Pessoa = fopen("Pessoa.txt", "ab");

    if(Pessoa == NULL){
        printf("Problemas ao abrir o arquivo!\n");
    } else{
            cabecalho();
            fflush(stdin);
            printf("Digite o nome da pessoa que deseja cadastrar: ");
            gets(gnt.nome);
            tranformar_minuscula(gnt.nome);


            fflush(stdin);
            printf("Digite o sexo dessa pessoa (masculino ou feminino): ");
            gets(gnt.sexo);
            tranformar_minuscula(gnt.sexo);
            if(strcmp(gnt.sexo, "masculino") != 0 && strcmp(gnt.sexo, "feminino") != 0){ //VERIFICA O SEXO
                printf("\nO sexo inserido é inválido, retornando ao menu de opções . . . \n");
                getchar();
                return;
            }

            fflush(stdin);
            printf("Digite a data de nascimento dessa pessoa no formato DD MM AAAA: ");
            scanf("%d %d %d", &gnt.aniv.dia, &gnt.aniv.mes, &gnt.aniv.ano);
            validez_data = valida_data(gnt.aniv.dia, gnt.aniv.mes, gnt.aniv.ano);
            if (validez_data == 0){ // VERIFICA A DATA
                printf("\nData incorreta!\n");
                printf("Voltando ao menu de opções . . .\n");
                getchar();
                getchar();
                return;
            }

            fflush(stdin);
            printf("Digite o curso desejado por essa pessoa: ");
            gets(gnt.curso_desejado);
            tranformar_minuscula(gnt.curso_desejado);
            achei_curso = procurar_curso(gnt.curso_desejado);
            if(achei_curso == 0){ //VERIFICA SE O CURSO JÁ FOI CADASTRADO NO ARQUIVO CURSO
                printf("\nNão foi possível incluir essa pessoa pois esse curso não foi cadastrado!");
                printf("\nVoltando ao menu de opções . . .\n");
                getchar();
                return;
            }

            fflush(stdin);
            printf("Digite o Estado dessa pessoa: ");
            gets(gnt.unid_fed);
            tranformar_minuscula(gnt.unid_fed);
            achei_estado = procurar_estado(gnt.unid_fed);
            if(achei_estado == 0){ //VERIFICA SE O ESTADO JÁ FOI CADASTRADO NO ARQUIVO ESTADO
                printf("\nNão foi possível incluir essa pessoa pois esse Estado não foi cadastrado!");
                printf("\nVoltando ao menu de opções . . .\n");
                getchar();
                return;
            }

            fflush(stdin);
            fwrite(&gnt, sizeof(PESSOA), 1, Pessoa);
            printf("\nPessoa cadastrada com sucesso!\n");
            printf("Voltando ao menu de opções . . .\n")/
            getchar();

            fclose(Pessoa);
    }
}

//OBJETIVO: RECEBER UMA STRING QUE VAI SER O NOME A PESQUISAR, PERCORRER O ARQUIVO PARA LISTAR
//OS DADOS DA PESSOA PROCURADA
void consultar_pessoa(){
    char nome_pesquisa[50];
    int achei = 0;
    FILE* Pessoa;
    PESSOA gente;


    Pessoa = fopen("Pessoa.txt", "rb");
    cabecalho();

    if(Pessoa == NULL){
        printf("Problemas na abertura do arquivo! ");
    } else{
        fflush(stdin);
        printf("Digite o nome da pessoa que deseja pesquisar: ");
        gets(nome_pesquisa);
        tranformar_minuscula(nome_pesquisa);

        while(fread(&gente, sizeof(PESSOA), 1, Pessoa) == 1){ //PERCORRE O ARQUIVO
            if(strstr(gente.nome, nome_pesquisa) != NULL){ //COMPARA AS DUAS STRINGS PERMITINDO A PROCURA POR PARTE DO NOME
                printf("\n---------------------------------------\n");
                printf("Nome completo: %s\n", gente.nome);
                printf("Sexo: %s\n", gente.sexo);
                printf("Data de nascimento: %d/%d/%d\n", gente.aniv.dia, gente.aniv.mes, gente.aniv.ano);
                printf("Curso desejado: %s\n", gente.curso_desejado);
                printf("Unidade da federação: %s\n", gente.unid_fed);
                printf("---------------------------------------\n");
                achei++;
            }
        }

        if(achei == 0){
            printf("\nPessoa não cadastrada! ");
            }
        fclose(Pessoa);
        printf("\nPressione qualquer tecla para continuar . . . ");
        getch();
    }
}
//RECEBE UMA STRING ESTADO E LISTA TODAS AS PESSOAS CADASTRADAS NESSE ESTADO
void listar_pessoas_estado(){
        char estado_listar[50];
        int achei = 0;
        FILE* Pessoa;
        Pessoa = fopen("Pessoa.txt", "rb");

        PESSOA gente;

        if(Pessoa == NULL){
            printf("Problemas ao abrir o arquivo! \n");
        } else {
            fflush(stdin);
            printf("Digite um Estado para listar as pessoas desse Estado: ");
            gets(estado_listar);
            tranformar_minuscula(estado_listar);

            while(fread(&gente, sizeof(PESSOA), 1, Pessoa) == 1){
                if(strcmp(estado_listar, gente.unid_fed) == 0){
                    printf("\n---------------------------------------\n");
                    printf("Nome completo: %s\n", gente.nome);
                    printf("Sexo: %s\n", gente.sexo);
                    printf("Data de nascimento: %d/%d/%d\n", gente.aniv.dia, gente.aniv.mes, gente.aniv.ano);
                    printf("Curso desejado: %s\n", gente.curso_desejado);
                    printf("Unidade da federação: %s\n", gente.unid_fed);
                    printf("---------------------------------------\n");
                    achei++;
                }
            }
            if(achei == 0){
                printf("\nEstado não cadastrado ou nenhuma pessoa inserida nesse curso!\n");
            }
            fclose(Pessoa);
            printf("Pressione enter para continuar . . .\n");
            getch();

        }
}

//RECEBE UMA STRING CURSO E LISTA TODAS AS PESSOAS CADASTRADAS NESSE CURSO
void listar_pessoas_curso(){ //FUNCIONANDO
        char curso_listar[50];
        int achei = 0;
        FILE* Pessoa;
        Pessoa = fopen("Pessoa.txt", "rb");

        PESSOA gente;

        if(Pessoa == NULL){
            printf("Problemas ao abrir o arquivo! \n");
        } else {
            fflush(stdin);
            printf("Digite um curso para listar as pessoas que desejam esse curso: ");
            gets(curso_listar);
            tranformar_minuscula(curso_listar);

            while(fread(&gente, sizeof(PESSOA), 1, Pessoa) == 1){
                if(strcmp(curso_listar, gente.curso_desejado) == 0){
                    printf("\n---------------------------------------\n");
                    printf("Nome completo: %s\n", gente.nome);
                    printf("Sexo: %s\n", gente.sexo);
                    printf("Data de nascimento: %d/%d/%d\n", gente.aniv.dia, gente.aniv.mes, gente.aniv.ano);
                    printf("Curso desejado: %s\n", gente.curso_desejado);
                    printf("Unidade da federação: %s\n", gente.unid_fed);
                    printf("---------------------------------------\n");
                    achei++;
                }
            }
            if(achei == 0){
                printf("\nCurso não cadastrado ou nenhuma pessoa inserida nesse curso!\n");
            }
            fclose(Pessoa);
            printf("Pressione enter para continuar . . .\n");
            getch();

        }
}

//RETORNA 1 CASO A DATA ESTEJA VALIDA OU RETORNA 0 CASO A DATA ESTEJA INVÁLIDA
int valida_data(int dia, int mes, int ano){
    int valido = 0;
        if(dia >= 1 && dia <= 31){
            if(mes == 1 || mes == 3 || mes == 5 || mes == 7 || mes == 8 || mes == 10 || mes == 12){
                if(ano >= 1900 && ano <= 2019){
                    valido = 1;
                }
            }
        }
        if(dia >= 1 && dia <= 30){
            if(mes == 4 || mes == 6 || mes == 9 || mes == 11){
                if(ano >= 1900 && ano <= 2019){
                    valido = 1;
                }
            }
        }
        if(dia >= 1 && dia <= 28){
            if(mes == 2){
                if((dia >= 1 && dia <= 31) && (mes >= 1 && mes <= 12) && (ano >= 1900 && ano <= 2100)){
                    valido = 1;
                }
            }
        }


           return valido;
}

//REMOVE PESSOAS COM COMPATIBILIDADE DE 100% DE DUAS STRINGS
//CRIA UM ARQUIVO AUXILIAR PARA ARMAZENAR TODOS OS DADOS DO PRIMEIRO ARQUIVO EXCETO
//A PESSOA QUE DESEJA REMOVER, NO FINAL RENOMEIA O ARQUIVO AUXILIAR COMO ARQUIVO
//PRINCIPAL
void remover_pessoas(){
    FILE* Pessoa;
    Pessoa = fopen("Pessoa.txt", "rb");
    FILE* Pessoa2;
    Pessoa2 = fopen("Pessoa2.txt", "wb");

    PESSOA gente;

    char nome_remover[TAM];
    char opcao;
    int achei = 0;

    if(Pessoa == NULL){
        printf("Problemas ao abrir o arquivo! \n");
    } else{
        fflush(stdin);
        printf("Digite o nome do candidato que deseja remover: ");
        gets(nome_remover);

        while(fread(&gente, sizeof(PESSOA), 1, Pessoa) == 1){
                if(strcmp(nome_remover, gente.nome) != 0){ //GRAVA TODOS AS STRUCTS QUE SÃO DIFERENTES DAS PESSOA QUE DESEJA REMOVER NO NOVO ARQUIVO
                    fwrite(&gente, sizeof(PESSOA), 1, Pessoa2);
            }
        }
    }

            fclose(Pessoa2);
            fclose(Pessoa);
            remove("Pessoa.txt");
            rename("Pessoa2.txt", "Pessoa.txt");

}

void relatorio_demografico(){
    float cont_masculino = 0, cont_feminino = 0, contar_total = 0; //VARIAVEIS PARA CONTAR HOMENS, MULHERES E O TOTAL
    float percentual_masculino, percentual_feminino; //ARMAZENAM A PORCENTAGEM DE HOMENS E MULHERRES
    float dia_atual, mes_atual, ano_atual; //RECEBE A DATA DO USUÁRIO
    int dia_pessoa[TAM], mes_pessoa[TAM], ano_pessoa[TAM]; //RECEBE A DATA DA PESSOA DENTRO DA STRUCT
    int data_valida; // VERIFICA SE A DATA QUE O USUARIO DIGITOU É VALIDA
    int i = 0, k = 0, j; //VARIAVEIS AUXILIARES PARA LAÇOS DE REPETIÇÃO
    float idade_pessoa[TAM]; // RECEBE A IDADE DA PESSOA DENTRO DA STRUCT PARA CALCULAR O PERCENTUAL
    float cont_grupo_1 = 0, cont_grupo_2 = 0, cont_grupo_3 = 0, cont_grupo_4 = 0; //CONTA A QUANTIDADE DE INDIVIDUOS SEPARADOS EM 4 GRUPOS PARA FAZER A PORCENTAGEM DELES
    float percentual_grupo_1, percentual_grupo_2, percentual_grupo_3, percentual_grupo_4; //CALCULA O PERCENTUAL DE INDIVIDUOS EM CADA GRUPO
    ANIVERSARIO aniversario[TAM]; //NOVA STRUCT EM FORMA DE ESTRUTURA HOMOGÊNEA AUXILIAR PARA ARMAZENAR AS DATAS DAS PESSOAS DENTRO DA STRUCT
    FILE* Pessoa;
    Pessoa = fopen("Pessoa.txt", "rb");

    PESSOA gente;
    fflush(stdin);

    if(Pessoa == NULL){
        printf("Problemas ao abrir o arquivo! \n");
    } else{
        while(fread(&gente, sizeof(PESSOA), 1, Pessoa) == 1){ //PERCORRER TODO O ARQUIVO
            aniversario[k].birthday.dia = gente.aniv.dia; //GRAVA O DIA NA STRUCT AUXILIAR QUE É UM VETOR
            aniversario[k].birthday.mes = gente.aniv.mes; //GRAVA O MES NA STRUCT AUXILIAR QUE É UM VETOR
            aniversario[k].birthday.ano = gente.aniv.ano; //GRAVA O ANO NA STRUCT AUXILIAR QUE É UM VETOR
            k++; //VARIAVEL AUXILIAR PARA GRAVAR EM CADA POSIÇÃO DO VETOR DE STRUCT
            if(strcmp(gente.sexo, "masculino") == 0){
                cont_masculino = cont_masculino + 1; //CONTAR OS HOMENS
            }

            if(strcmp(gente.sexo, "feminino") == 0){
                cont_feminino = cont_feminino + 1; //CONTAR AS MULHERES
            }
        }
        contar_total = cont_feminino + cont_masculino; //CONTAR O TOTAL DE PESSOAS COM BASE NA SOMA DE HOMENS E MULHERES

        if(contar_total == 0){ //SE FOR IGUAL A 0 SIGNIFICA QUE NENHUMA PESSOA FOI CADASTRADA E VOLTA AO MENU DE OPÇÕES
            printf("\nNenhuma pessoa cadastrada para fazer relatório!");
            printf("\nVoltando ao menu de opções . . .");
            getchar();
            return;
        }

        printf("Insira a data de hoje para prosseguir com o relatório: "); //RECEBE A DATA ATUAL DO USUÁRIO
        scanf("%f %f %f", &dia_atual, &mes_atual, &ano_atual);
        data_valida = valida_data(dia_atual, mes_atual, ano_atual); //VERIFICA SE A DATA RECEBIDA PELO USUÁRIO É VÁLIDA

        if(data_valida == 0){ //SE FOR INVÁLIDA AQUI O RELATÓRIO SERÁ INTERROMPIDO
            printf("\nPor favor, insira uma data válida!");
            printf("\nVoltando ao menu de opções . . .");
            getchar();
            getchar();
            return;
        }


        percentual_masculino = (cont_masculino / contar_total) * 100; //CÁLCULO ALGÉBRICO DO PERCENTUAL MASCULINO
        percentual_feminino = (cont_feminino / contar_total) * 100; //CÁLCULO ALGÉBRICO DO PERCENTUAL FEMININO

        for (j = 0; j < contar_total; j++){
            idade_pessoa[j] = 2019 - aniversario[j].birthday.ano; //VERIFICA QUANTOS ANOS CADA PESSOA TEM
        }
        for(j = 0; j < contar_total; j++){
            if(idade_pessoa[j] >= 15 && idade_pessoa[j] <= 20){ //CONTA QUANTAS PESSOAS TEM ENTRE 15 E 20 ANOS
                cont_grupo_1++;
            }
            if(idade_pessoa[j] >= 21 && idade_pessoa[j] <= 25){ //CONTA QUANTAS PESSOAS TEM ENTRE 21 E 25 ANOS
                cont_grupo_2++;
            }
            if(idade_pessoa[j] >= 26 && idade_pessoa[j] <= 30){ //CONTA QUANTAS PESSOAS TEM ENTRE 26 E 30 ANOS
                cont_grupo_3++;
            }
            if(idade_pessoa[j] >= 30){ //CONTA QUANTAS PESSOAS SÃO MAIORES DE 30 ANOS
                cont_grupo_4++;
            }
        }
        //CALCULA O PERCENTUAL DE PESSOA DE CADA GRUPO
        percentual_grupo_1 = (cont_grupo_1 / contar_total) * 100;
        percentual_grupo_2 = (cont_grupo_2 / contar_total) * 100;
        percentual_grupo_3 = (cont_grupo_3 / contar_total) * 100;
        percentual_grupo_4 = (cont_grupo_4 / contar_total) * 100;

        //PRINTA OS DADOS OBTIDOS POR MEIO DESSE RELATÓRIO
        printf("\nDo total de candidatos inscritos: ");
        printf("\n\t%.2f%% têm idade entre 15 e 20 anos", percentual_grupo_1);
        printf("\n\t%.2f%% têm idade entre 21 e 25 anos", percentual_grupo_2);
        printf("\n\t%.2f%% têm idade entre 26 e 30 anos", percentual_grupo_3);
        printf("\n\te %.2f%% são maiores de 30 anos", percentual_grupo_4);


        printf("\n\nO percentual de homens que fizeram o Enem foi de %.2f%%\n", percentual_masculino);
        printf("O percentual de mulheres que fizeram o Enem foi de %.2f%%\n", percentual_feminino);
        getchar();
        getchar();

    }

}
