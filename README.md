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
	 int i;	
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
	
	printf("\n\n"); 
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

	int i, j;
	printf("\nACTIVIDAD MAS POPULAR POR CENTRO:\n");
	for(i= 0; i< nRegistros; i++){
		int yaprocesado = 0;
		for(j= 0; j< i; j++){
			if(strcmp(registros[i].centro, registros[j].centro) ==0)
			{
			   yaprocesado= 1;
			   break;
			}
		}
		if (!yaprocesado) {
		int maxocupadas = -1;
		char* actividadpopular = NULL;
		for (j= 0; j< nRegistros; j++) {
		if (strcmp(registros[i].centro, registros[j].centro) ==0) {
			if (registros[j].ocupadas > maxocupadas) {
				maxocupadas = registros[j].ocupadas;
				actividadpopular= registros[j].actividad_base;
				}
			}
		}
		printf("- Centro: %s | Actividad mas popular: %s (%d plazas ocupadas)\n",
		registros[i].centro, actividadpopular, maxocupadas);
		}
	}
}
void ActividadMenosPopularPorCentro(ActividadDeportiva* registros, int nRegistros) {

    int i, j;
	printf("\nACTIVIDAD MENOS POPULAR POR CENTRO:\n");
	 	for (i = 0; i < nRegistros; i++) {
        int yaprocesado = 0;

        for (j = 0; j < i; j++) {
            if (strcmp(registros[i].centro, registros[j].centro) == 0) {
                yaprocesado = 1;
                break;
            }
        }

        if (!yaprocesado) {
            int minocupadas = -1;
            char* actividadmenos = NULL;

            for (j = 0; j < nRegistros; j++) {
                if (strcmp(registros[i].centro, registros[j].centro) == 0) {
                    if (minocupadas == -1 || registros[j].ocupadas < minocupadas) {
                        minocupadas = registros[j].ocupadas;
                        actividadmenos = registros[j].actividad_base;
                    }
                }
            }

            printf("- Centro: %s | Actividad menos popular: %s (%d plazas ocupadas)\n",
                registros[i].centro,actividadmenos, minocupadas);
        }
    }
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



void HorasMasConcurridasPorCentro(ActividadDeportiva* registros, int nRegistros) {

    int i;
    char centroBuscado[100];

    int maxOcupadas = -1;
    char* horaMasConcurridaInicio = NULL;
    char* horaMasConcurridaFin = NULL;
    char* actividad = NULL;

    printf("\nIntroduce el nombre del centro deportivo: ");
    fgets(centroBuscado, sizeof(centroBuscado), stdin);
    centroBuscado[strcspn(centroBuscado, "\n")] = 0;

    for (i = 0; i < nRegistros; i++) {
        if (strcmp(registros[i].centro, centroBuscado) == 0) {

            if (registros[i].ocupadas > maxOcupadas) {
                maxOcupadas = registros[i].ocupadas;
                horaMasConcurridaInicio = registros[i].hora_inicio;
                horaMasConcurridaFin = registros[i].hora_fin;
                actividad = registros[i].actividad_base;
            }
        }
    }

    if (maxOcupadas == -1) {
        printf("No se encontro ningun registro para ese centro.\n");
    } else {
        printf("\nHORA MAS CONCURRIDA DEL CENTRO '%s'\n", centroBuscado);
        printf("Hora: %s - %s\n", horaMasConcurridaInicio, horaMasConcurridaFin);
        printf("Actividad: %s\n", actividad);
        printf("Plazas ocupadas: %d\n", maxOcupadas);
    }
}

typedef struct {
    
	char nombre[200];
	int total_ocupadas;
	
} ResumenCentro;

void top_centros_mas_concurridos(ActividadDeportiva* registros, int n_registros) {
    
	int i, j;
    int n_centros = 0;

    ResumenCentro* centros = (ResumenCentro*)malloc(n_registros * sizeof(ResumenCentro));
    if (centros == NULL) {
        printf("Error: Memoria insuficiente.\n");
        return;
    }

    for (i = 0; i < n_registros; i++) {
        int encontrado = 0;
        for (j = 0; j < n_centros; j++) {
            if (strcmp(centros[j].nombre, registros[i].centro) == 0) {
                centros[j].total_ocupadas += registros[i].ocupadas;
                encontrado = 1;
                break;
            }
        }
        if (encontrado == 0) {
            strcpy(centros[n_centros].nombre, registros[i].centro);
            centros[n_centros].total_ocupadas = registros[i].ocupadas;
            n_centros++;
        }
    }

    ResumenCentro temp;
    for (i = 0; i < n_centros - 1; i++) {
        for (j = 0; j < n_centros - i - 1; j++) {
            if (centros[j].total_ocupadas < centros[j+1].total_ocupadas) {
                temp = centros[j];
                centros[j] = centros[j +1];
                centros[j + 1] = temp;
            }
        }
    }

    int listado = 10;
    if (n_centros < 10) {
        listado = n_centros;
    }

    printf("\nTOP %d CENTROS MAS CONCURRIDOS\n", listado);
    for (i = 0; i < listado; i++) {
        printf("%d. %s (%d plazas ocupadas)\n", i + 1, centros[i].nombre, centros[i].total_ocupadas);
    }

    free(centros);
}

void top_centros_menos_concurridos(ActividadDeportiva* registros, int n_registros) {
	
	int i, j;
    int n_centros = 0;

    ResumenCentro* centros = (ResumenCentro*)malloc(n_registros * sizeof(ResumenCentro));
    if (centros == NULL) {
        printf("Error: Memoria insuficiente.\n");
        return;
    }

    for (i = 0; i < n_registros; i ++) {
        int encontrado = 0;
        for (j = 0; j < n_centros; j ++) {
            if (strcmp(centros[j].nombre, registros[i].centro) == 0) {
                centros[j].total_ocupadas += registros[i].ocupadas;
                encontrado = 1;
                break;
            }
        }
        if (encontrado == 0) {
            strcpy(centros[n_centros].nombre, registros[i].centro);
            centros[n_centros].total_ocupadas = registros[i].ocupadas;
            n_centros++;
        }
    }

    ResumenCentro temp;
    for (i = 0; i < n_centros - 1; i ++) {
        for (j = 0; j < n_centros - i - 1; j ++) {
            if (centros[j].total_ocupadas > centros[j+1].total_ocupadas) {
                temp = centros[j];
                centros[j] = centros[j + 1];
                centros[j + 1] = temp;
            }
        }
    }

    int listado = 10;
    if (n_centros < 10) {
        listado = n_centros;
    }

    printf("\nTOP %d CENTROS MENOS CONCURRIDOS\n", listado);
    for (i= 0; i < listado; i++) {
        printf("%d. %s (%d plazas ocupadas)\n", i +1, centros[i].nombre, centros[i].total_ocupadas);
    }

    free(centros);
}
void Top10ActividadesConMasDemanda(ActividadDeportiva* registros, int nRegistros) {
    int i, j;

    int usados[10];
    for (i = 0; i < 10; i++) {
        usados[i] = -1;
    }

    printf("\nTOP 10 ACTIVIDADES CON MAS DEMANDA:\n");

    for (i = 0; i < 10 && i < nRegistros; i++) {
        int maxocupadas = -1;
        int indiceMax = -1;

        for (j = 0; j < nRegistros; j++) {
            int yaUsado = 0;
            int k;

            for (k = 0; k < i; k++) {
                if (usados[k] == j) {
                    yaUsado = 1;
                    break;
                }
            }

            if (!yaUsado && registros[j].ocupadas > maxocupadas) {
                maxocupadas = registros[j].ocupadas;
                indiceMax = j;
            }
        }

        if (indiceMax != -1) {
            usados[i] = indiceMax;
            printf("%d. %s | Centro: %s (%d plazas ocupadas)\n",
                   i + 1,
                   registros[indiceMax].actividad_base,
                   registros[indiceMax].centro,
                   registros[indiceMax].ocupadas);
        }
    }
}

void DiasMasConcurridosPorCentro(ActividadDeportiva* registros, int nRegistros) {
    
    int i;
    char centroBuscado[100];

    int lunes = 0;
    int martes = 0;
    int miercoles = 0;
    int jueves = 0;
    int viernes = 0;
    int sabado = 0;
    int domingo = 0;

    int maxOcupadas;
    char* diaMasConcurrido;

    printf("\nIntroduce el nombre del centro deportivo: ");
    fgets(centroBuscado, sizeof(centroBuscado), stdin);
    centroBuscado[strcspn(centroBuscado, "\n")] = 0;

    for (i = 0; i < nRegistros; i++) {
        if (strcmp(registros[i].centro, centroBuscado) == 0) {

            if (strcmp(registros[i].dia_semana, "lunes") == 0) {
                lunes = lunes + registros[i].ocupadas;
            }
            else if (strcmp(registros[i].dia_semana, "martes") == 0) {
                martes = martes + registros[i].ocupadas;
            }
            else if (strcmp(registros[i].dia_semana, "miercoles") == 0) {
                miercoles = miercoles + registros[i].ocupadas;
            }
            else if (strcmp(registros[i].dia_semana, "jueves") == 0) {
                jueves = jueves + registros[i].ocupadas;
            }
            else if (strcmp(registros[i].dia_semana, "viernes") == 0) {
                viernes = viernes + registros[i].ocupadas;
            }
            else if (strcmp(registros[i].dia_semana, "sabado") == 0) {
                sabado = sabado + registros[i].ocupadas;
            }
            else if (strcmp(registros[i].dia_semana, "domingo") == 0) {
                domingo = domingo + registros[i].ocupadas;
            }
        }
    }

    maxOcupadas = lunes;
    diaMasConcurrido = "lunes";

    if (martes > maxOcupadas) {
        maxOcupadas = martes;
        diaMasConcurrido = "martes";
    }

    if (miercoles > maxOcupadas) {
        maxOcupadas = miercoles;
        diaMasConcurrido = "miercoles";
    }

    if (jueves > maxOcupadas) {
        maxOcupadas = jueves;
        diaMasConcurrido = "jueves";
    }

    if (viernes > maxOcupadas) {
        maxOcupadas = viernes;
        diaMasConcurrido = "viernes";
    }

    if (sabado > maxOcupadas) {
        maxOcupadas = sabado;
        diaMasConcurrido = "sabado";
    }

    if (domingo > maxOcupadas) {
        maxOcupadas = domingo;
        diaMasConcurrido = "domingo";
    }

    if (maxOcupadas == 0) {
        printf("No se encontro ningun registro para ese centro.\n");
    }
    else {
        printf("\nDIA MAS CONCURRIDO DEL CENTRO '%s'\n", centroBuscado);
        printf("Dia mas concurrido: %s\n", diaMasConcurrido);
        printf("Total de plazas ocupadas ese dia: %d\n", maxOcupadas);

        printf("\nResumen semanal del centro:\n");
        printf("Lunes:     %d plazas ocupadas\n", lunes);
        printf("Martes:    %d plazas ocupadas\n", martes);
        printf("Miercoles: %d plazas ocupadas\n", miercoles);
        printf("Jueves:    %d plazas ocupadas\n", jueves);
        printf("Viernes:   %d plazas ocupadas\n", viernes);
        printf("Sabado:    %d plazas ocupadas\n", sabado);
        printf("Domingo:   %d plazas ocupadas\n", domingo);
    }
}

void CentrosMasSaturados(ActividadDeportiva* registros, int nRegistros) {

    int i, j;
    int nCentros = 0;

    ResumenCentro* centros = (ResumenCentro*)malloc(nRegistros * sizeof(ResumenCentro));
    float porcentajes[1000];
    int plazasLibres[1000];
    int totalPlazas[1000];

    if (centros == NULL) {
        printf("Error: Memoria insuficiente.\n");
        return;
    }

    for (i = 0; i < nRegistros; i++) {

        int encontrado = 0;

        for (j = 0; j < nCentros; j++) {

            if (strcmp(centros[j].nombre, registros[i].centro) == 0) {

                centros[j].total_ocupadas = centros[j].total_ocupadas + registros[i].ocupadas;
                plazasLibres[j] = plazasLibres[j] + registros[i].libres;
                totalPlazas[j] = totalPlazas[j] + registros[i].plazas;

                encontrado = 1;
                break;
            }
        }

        if (encontrado == 0) {

            strcpy(centros[nCentros].nombre, registros[i].centro);

            centros[nCentros].total_ocupadas = registros[i].ocupadas;
            plazasLibres[nCentros] = registros[i].libres;
            totalPlazas[nCentros] = registros[i].plazas;

            nCentros++;
        }
    }

    for (i = 0; i < nCentros; i++) {
        if (totalPlazas[i] > 0) {
            porcentajes[i] = (centros[i].total_ocupadas * 100.0) / totalPlazas[i];
        }
        else {
            porcentajes[i] = 0;
        }
    }

    for (i = 0; i < nCentros - 1; i++) {
        for (j = 0; j < nCentros - i - 1; j++) {

            if (porcentajes[j] < porcentajes[j + 1]) {

                float tempPorcentaje = porcentajes[j];
                porcentajes[j] = porcentajes[j + 1];
                porcentajes[j + 1] = tempPorcentaje;

                int tempLibres = plazasLibres[j];
                plazasLibres[j] = plazasLibres[j + 1];
                plazasLibres[j + 1] = tempLibres;

                int tempPlazas = totalPlazas[j];
                totalPlazas[j] = totalPlazas[j + 1];
                totalPlazas[j + 1] = tempPlazas;

                ResumenCentro tempCentro = centros[j];
                centros[j] = centros[j + 1];
                centros[j + 1] = tempCentro;
            }
        }
    }

    printf("\nTOP 5 CENTROS MAS SATURADOS\n");

    int listado = 5;

    if (nCentros < 5) {
        listado = nCentros;
    }

    for (i = 0; i < listado; i++) {

        printf("%d. %s\n", i + 1, centros[i].nombre);
        printf("   Plazas libres: %d\n", plazasLibres[i]);
        printf("   Porcentaje de ocupacion: %.2f%%\n", porcentajes[i]);
    }

    free(centros);
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
	  printf("\n \n \n");
      printf("\n    MENU DE OPCIONES    \n");
      printf("1. Listado de actividades por centro deportivo\n");
      printf("2. Frecuencia diaria de las actividades ofertadas\n");
      printf("3. Identificar actividad más popular por centro deportivo\n");
      printf("4. Identificar actividad menos popular por centro deportivo\n");
	  printf("5. Actividad y hora con mas ocupacion por centro deportivo\n");
 	  printf("6. Top centros mas concurridos\n");
	  printf("7. Top centros menos concurridos\n");
	  printf("8. Top 10 actividades con mas demanda\n");
	  printf("9. Top 5 centros mas saturados\n");
	  printf("10. Salir\n");
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
			case 4:
				ActividadMenosPopularPorCentro(registros,nRegistros);
				break;
			 case 5:
                HorasMasConcurridasPorCentro(registros, nRegistros);
                break;
			case 6:
   				top_centros_mas_concurridos(registros, nRegistros);
    			break;
			case 7:
   		 		top_centros_menos_concurridos(registros, nRegistros);
    			break;
			case 8:
				Top10ActividadesConMasDemanda(registros,nRegistros);
				break;
			case 9:
				CentrosMasSaturados(registros, nRegistros);
				break;
	        case 10:
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
