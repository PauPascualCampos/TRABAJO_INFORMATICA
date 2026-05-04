#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <locale.h>


typedef struct {
    int anio;
    int mes;
    int dia;
    char* dia_semana;
    char* hora_inicio;
    char* hora_fin;
    char* actividad_base;
    char* modalidad;
    char* centro;
    int plazas;
    int ocupadas;
    int libres;
    char* tipo_actividad;
} ActividadDeportiva;

int contarLineas(const char* nombreFichero) {
    FILE* f = fopen(nombreFichero, "r");
    if (!f) return -1;
    
    int contador = 0;
    char buffer[1024];
    while (fgets(buffer, sizeof(buffer), f)) {
        contador++;
    }
    fclose(f);
    return contador - 1;
}


ActividadDeportiva* cargarDatos(const char* nombreFichero, int* nRegistros) {
    FILE* f = fopen(nombreFichero, "r");
    if (!f) return NULL;

    *nRegistros = contarLineas(nombreFichero);
    if (*nRegistros <= 0) {
        fclose(f);
        return NULL;
    }

    char buffer[1024];
    fgets(buffer, sizeof(buffer), f);

    ActividadDeportiva* registros = (ActividadDeportiva*)malloc(*nRegistros * sizeof(ActividadDeportiva));
    if (!registros) {
        fclose(f);
        return NULL;
    }

    int i = 0; 
    while (fgets(buffer, sizeof(buffer), f) && i < *nRegistros) {
        buffer[strcspn(buffer, "\n")] = 0;
        buffer[strcspn(buffer, "\r")] = 0;

        char* carac = strtok(buffer, " \t");
        if (carac) {
            registros[i].anio = atoi(carac);

            carac = strtok(NULL, " \t");
            registros[i].mes = carac ? atoi(carac) : 0;

            carac = strtok(NULL, " \t");
            registros[i].dia = carac ? atoi(carac) : 0;

            carac = strtok(NULL, " \t");
            registros[i].dia_semana = carac ? strdup(carac) : strdup("");

            carac = strtok(NULL, " \t");
            registros[i].hora_inicio = carac ? strdup(carac) : strdup("");

            carac = strtok(NULL, " \t");
            registros[i].hora_fin = carac ? strdup(carac) : strdup("");

            carac = strtok(NULL, " \t");
            registros[i].actividad_base = carac ? strdup(carac) : strdup("");

            carac = strtok(NULL, " \t");
            registros[i].modalidad = carac ? strdup(carac) : strdup("");

            carac = strtok(NULL, " \t");
            registros[i].centro = carac ? strdup(carac) : strdup("");

            carac = strtok(NULL, " \t");
            registros[i].plazas = carac ? atoi(carac) : 0;

            carac = strtok(NULL, " \t");
            registros[i].ocupadas = carac ? atoi(carac) : 0;

            carac = strtok(NULL, " \t");
            registros[i].libres = carac ? atoi(carac) : 0;

            carac = strtok(NULL, " \t");
            registros[i].tipo_actividad = carac ? strdup(carac) : strdup("");

            i++;
        }
    }

    *nRegistros = i; 
    fclose(f);
    return registros;
}

void FrecuenciaDiariaActividadesOfertadas(ActividadDeportiva* registros, int nRegistros) {

     int lunes = 0;
	 int martes = 0;
	 int miercoles = 0;
	 int jueves = 0;
	 int viernes = 0;
	 int sabado = 0;
	 int domingo = 0;
	 
	 for (i = 0; i < nRegistros; i++) {
	 	if (strcmp(registros[i].dia_semana, "lunes") == 0)
            lunes++;
        else if (strcmp(registros[i].dia_semana, "martes") == 0)
            martes++;
     	else if (strcmp(registros[i].dia_semana, "miercoles") == 0)
            miercoles++;
        else if (strcmp(registros[i].dia_semana, "jueves") == 0)
            jueves++;
        else if (strcmp(registros[i].dia_semana, "viernes") == 0)
            viernes++;
        else if (strcmp(registros[i].dia_semana, "sabado") == 0)
            sabado++;
        else if (strcmp(registros[i].dia_semana, "domingo") == 0)
            domingo++;
    }
	
	printf("\n\n") 
	printf("Frequencia diaria de ACTIVIDADES OFERTADAS: \n");
    printf("Lunes:     %d actividades\n", lunes);
    printf("Martes:    %d actividades\n", martes);
    printf("Miercoles: %d actividades\n", miercoles);
    printf("Jueves:    %d actividades\n", jueves);
    printf("Viernes:   %d actividades\n", viernes);
    printf("Sabado:    %d actividades\n", sabado);
    printf("Domingo:   %d actividades\n", domingo);
	 	
}

void ActividadMasPopularPorCentro(ActividadDeportiva* registros, int nRegistros) {


}


void liberarDatos(ActividadDeportiva* registros, int nRegistros) {
    int i;
    if (!registros) return;
    for (i = 0; i < nRegistros; i++) {
        free(registros[i].dia_semana);
        free(registros[i].hora_inicio);
        free(registros[i].hora_fin);
        free(registros[i].actividad_base);
        free(registros[i].modalidad);
        free(registros[i].centro);
        free(registros[i].tipo_actividad);
    }
    free(registros);
}


void ListarActividadesPorCentro(ActividadDeportiva* registros, int nRegistros) {
    int i;
    char centroBuscado[100];
    printf("\nIntroduce el nombre del centro deportivo: ");
    fgets(centroBuscado, sizeof(centroBuscado), stdin);
    centroBuscado[strcspn(centroBuscado, "\n")] = 0;

    printf("\n   - Actividades en el centro '%s' -\n", centroBuscado);
    int encontradas = 0;
    
    for (i = 0; i < nRegistros; i++) {
        if (strcmp(registros[i].centro, centroBuscado) == 0) {
            printf("- %s (Modalidad: %s) | %s a %s\n", registros[i].actividad_base, registros[i].modalidad, registros[i].hora_inicio, registros[i].hora_fin);
            encontradas = 1;
        }
    }
    
    if (encontradas == 0) {
        printf("No se encontraron actividades para ese centro.\n");
    }
}



int main() {
    setlocale(LC_ALL, "Spanish");

    ActividadDeportiva* registros = NULL;
    int nRegistros = 0;
    char nombreFichero[100];
    
    printf(" BIENVENIDO AL PROGRAMA DE ANALISIS DE CENTROS DEPORTIVOS \n");
    printf("\n Introduce el nombre del fichero (dataset.csv): ");
    
    fgets(nombreFichero, sizeof(nombreFichero), stdin);
    nombreFichero[strcspn(nombreFichero, "\n")] = 0;

    registros = cargarDatos(nombreFichero, &nRegistros);
    if (!registros) {
        printf("No se pudo abrir el archivo o esta vacio.\n");
        return 1;
    }

    printf("Archivo cargado correctamente (%d registros).\n", nRegistros);


char continuar = 's';
    while (continuar == 's' || continuar == 'S') {
      int opcion;
      printf("\n    MENU DE OPCIONES    \n");
      printf("1. Listado de actividades por centro deportivo\n");
      printf("2. Frecuencia diaria de las actividades ofertadas\n");
      printf("3. Identificar actividad más popular por centro deportivo\n");
      printf("4. func4\n");
	  printf("5. func5\n");
 	  printf("6. func6\n");
      printf("7. func7\n");
      printf("8. Salir\n");
      printf("Selecciona una opcion: ");
      scanf("%d", &opcion);
      getchar();

      switch (opcion) {
            case 1:
                ListarActividadesPorCentro(registros, nRegistros);
                break;
            case 2:
                FrecuenciaDiariaActividadesOfertadas(registros,nRegistros);
                break;
            case 3:
                ActividadMasPopularPorCentro(registros,nRegistros);
                break;
	        case 8:
		        liberarDatos(registros, nRegistros);
                printf("Programa finalizado.\n");
                system("pause");
                return 0;
            default:
                printf("Opcion no valida.\n");
        }

        printf("\n¿Deseas ejecutar otra funcion?: ");
        scanf(" %c", &continuar);
        getchar();
    }
}
