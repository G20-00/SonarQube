# Integración de SonarCloud con Proyecto GitHub

Esta guía proporciona un proceso detallado para integrar SonarCloud con tu proyecto GitHub y realizar un análisis de código. Usaremos el método de análisis manual debido a la ausencia de GitHub Actions o herramientas CI similares en el proyecto.

## Configuración de Docker Compose para SonarQube y PostgreSQL

Este `docker-compose.yml` configura un entorno con SonarQube y PostgreSQL. Puedes copiar y pegar el siguiente código en tu archivo `docker-compose.yml`.

```yaml
version: '2'

services:
  sonarqube:
    image: sonarqube
    ports:
      - "9000:9000"
    networks:
      - sonarnet
    environment:
      - SONARQUBE_JDBC_URL=jdbc:postgresql://db:5432/sonar
      - SONARQUBE_JDBC_USERNAME=sonar
      - SONARQUBE_JDBC_PASSWORD=sonar
    volumes:
      - sonarqube_conf:/opt/sonarqube/conf
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_bundled-plugins:/opt/sonarqube/lib/bundled-plugins

  db:
    image: postgres
    networks:
      - sonarnet
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

networks:
  sonarnet:
    driver: bridge

volumes:
  sonarqube_conf:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_bundled-plugins:
  postgresql:
  postgresql_data:
```

### Descripción

- **SonarQube**:
  - Imagen: `sonarqube`
  - Puerto: `9000`
  - Redes: `sonarnet`
  - Volúmenes:
    - `sonarqube_conf`: Configuración de SonarQube.
    - `sonarqube_data`: Datos de SonarQube.
    - `sonarqube_extensions`: Extensiones de SonarQube.
    - `sonarqube_bundled-plugins`: Plugins integrados de SonarQube.

- **PostgreSQL**:
  - Imagen: `postgres`
  - Redes: `sonarnet`
  - Volúmenes:
    - `postgresql`: Datos del sistema PostgreSQL.
    - `postgresql_data`: Datos de la base de datos.

- **Redes**:
  - `sonarnet`: Red de tipo `bridge` para la comunicación entre SonarQube y PostgreSQL.

- **Volúmenes**:
  - `sonarqube_conf`: Para almacenar la configuración de SonarQube.
  - `sonarqube_data`: Para almacenar los datos de SonarQube.
  - `sonarqube_extensions`: Para almacenar las extensiones de SonarQube.
  - `sonarqube_bundled-plugins`: Para almacenar los plugins integrados de SonarQube.
  - `postgresql`: Para almacenar los datos del sistema PostgreSQL.
  - `postgresql_data`: Para almacenar los datos de la base de datos PostgreSQL.

1. **Crear el archivo `docker-compose.yml`**: 
   Crea un archivo llamado `docker-compose.yml` en el directorio raíz de tu proyecto y copia el contenido anterior en él.

2. **Iniciar los contenedores**: 
   En una terminal, navega hasta el directorio donde se encuentra el archivo `docker-compose.yml` y ejecuta el siguiente comando para iniciar los servicios de SonarQube y PostgreSQL:

   ```sh
   docker-compose up -d

## Pasos para Integrar y Analizar tu Proyecto con SonarCloud

1. **Crea una Cuenta en SonarCloud e Inicia Sesión:**
   - Visita [SonarCloud](https://sonarcloud.io) y regístrate o inicia sesión en tu cuenta.

2. **Genera un Token de SonarCloud:**
   - Navega a la configuración de tu cuenta en la esquina superior derecha y selecciona "Mi cuenta".
   - En el menú de la izquierda, selecciona "Tokens de seguridad".
   - Haz clic en "Generar nuevo token", ingresa un nombre para tu token y haz clic en "Generar".
   - Guarda este token de manera segura ya que lo necesitarás para la autenticación. **Nota:** No podrás ver este token nuevamente, así que asegúrate de copiarlo y guardarlo en un lugar seguro.

   <p align="left">
  <img src="Img/1.png" alt="captura" width="400" height="250" />
</p>


3. **Configura el Proyecto en SonarCloud:**
   - Navega a "Mis Proyectos" y haz clic en "Analizar nuevo proyecto".
   
  <p align="left">
  <img src="Img/2.png" alt="captura" width="400" height="250" />
</p>

   - Elige importar una organización desde GitHub y concede los permisos necesarios. Este paso requiere que inicies sesión con tu cuenta de GitHub y autorices a SonarCloud a acceder a tus repositorios.

  <p align="left">
  <img src="Img/3.png" alt="captura" width="400" height="250" />
</p>

   - Selecciona el repositorio que deseas analizar (en este caso, `Integrador3`).

  <p align="left">
  <img src="Img/5.png" alt="captura" width="400" height="250" />
</p>

4. **Elige un Plan:**
   - Selecciona un plan gratuito o de pago según tus necesidades. Para repositorios privados, se requiere un plan de pago.

   <p align="left">
  <img src="Img/4.png" alt="captura" width="400" height="250" />
</p>

5. **Configura el Método de Análisis:**
   - Dado que el proyecto no utiliza GitHub Actions u otras herramientas CI, selecciona el método de análisis "Manual".

  <p align="left">
  <img src="Img/7.png" alt="captura" width="400" height="250" />
</p>
6. **Instala y Configura SonarScanner:**
   - Descarga e instala SonarScanner desde [SonarScanner Download](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/).
   - Asegúrate de agregar SonarScanner a tu PATH para poder ejecutarlo desde cualquier lugar en tu terminal.
   - Crea un archivo de configuración (`sonar-project.properties`) en la raíz de tu proyecto con el siguiente contenido:

    ```properties
    sonar.projectKey=G20-00_Integrador3
    sonar.organization=g20-00
    sonar.host.url=https://sonarcloud.io
    sonar.login=TU_SONAR_TOKEN
    sonar.sources=.
    sonar.exclusions=**/*.java
    ```
     Aquí hay una explicación de cada opción utilizada en este comando:

   - `sonar-scanner`: Este es el comando principal para ejecutar SonarScanner.
   - `-Dsonar.organization=g20-00`: Especifica la organización en SonarCloud. En este caso, `g20-00`.
   - `-Dsonar.projectKey=G20-00_Integrador3`: Define la clave del proyecto. Esto debe ser único dentro de la organización.
   - `-Dsonar.sources=.`: Indica el directorio raíz de las fuentes que se analizarán. Aquí, el punto `.` representa el directorio actual.
   - `-Dsonar.exclusions=**/*.java`: Excluye ciertos archivos o directorios del análisis. En este ejemplo, se excluyen todos los archivos `.java`.
   - `-Dsonar.host.url=https://sonarcloud.io`: Define la URL del servidor SonarCloud.

   <p align="left">
  <img src="Img/11.png" alt="captura" width="400" style="height: auto;" />
</p>

7. **Ejecuta SonarScanner:**
   - Abre una terminal y navega a tu directorio de proyecto.
   - Ejecuta el siguiente comando:

    ```sh
    sonar-scanner -Dsonar.login=TU_SONAR_TOKEN
    ```
   - `-Dsonar.login=$SONAR_TOKEN`: Este comando utiliza el token generado para autenticarte en SonarCloud. Asegúrate de que la variable de entorno `SONAR_TOKEN` contenga tu token de SonarCloud.
   

  <p align="left">
  <img src="Img/10.png" alt="captura" width="400" style="height: auto;" />
</p>


8. **Visualiza los Resultados del Análisis:**
   - Después de que el análisis se complete, navega al panel de tu proyecto en SonarCloud para ver los resultados.

   <p align="left">
  <img src="Img/13.png" alt="captura" width="400" height="250" />
</p>

   <p align="left">
  <img src="Img/14.png" alt="captura" width="400" height="250" />
</p>

## Resumen del Análisis

1. **Seguridad (Security)**
   - **0** problemas abiertos (Open issues).
   - Calificación: **A** (excelente).

2. **Fiabilidad (Reliability)**
   - **7** problemas abiertos.
   - **0** problemas críticos (H), **7** problemas mayores (M), **0** problemas menores (L).
   - Calificación: **C** (aceptable).

3. **Mantenibilidad (Maintainability)**
   - **575** problemas abiertos.
   - **55** problemas críticos (H), **520** problemas mayores (M), **0** problemas menores (L).
   - Calificación: **A** (excelente).

4. **Problemas Aceptados (Accepted Issues)**
   - **0** problemas aceptados.

5. **Cobertura (Coverage)**
   - **0.0%** cobertura de código.
   - No hay condiciones establecidas para cubrir las 16 líneas identificadas.

6. **Duplicaciones (Duplications)**
   - **34.9%** duplicación de código.
   - No hay condiciones establecidas sobre 8.1k líneas de código.

7. **Puntos Calientes de Seguridad (Security Hotspots)**
   - **0** puntos calientes de seguridad.

## Análisis Detallado

1. **Seguridad:**
   - Excelente resultado ya que no se encontraron problemas de seguridad abiertos. Esto indica que el código es seguro y no presenta vulnerabilidades conocidas.

2. **Fiabilidad:**
   - La calificación C indica que hay margen de mejora en la fiabilidad del código. Aunque no hay problemas críticos, los 7 problemas mayores deben ser revisados y corregidos para mejorar la estabilidad del proyecto.

3. **Mantenibilidad:**
   - A pesar de tener una calificación A, hay un número significativo de problemas abiertos (575), la mayoría de ellos mayores (520). Estos problemas de mantenibilidad pueden incluir código complejo o mal estructurado que debe ser refactorizado para mejorar la legibilidad y la facilidad de mantenimiento.

4. **Cobertura:**
   - La cobertura de código es del 0.0%, lo cual es preocupante. La falta de pruebas unitarias puede llevar a problemas no detectados durante el desarrollo. Es crucial implementar pruebas para cubrir el código y asegurar su correcto funcionamiento.

5. **Duplicaciones:**
   - Un alto porcentaje de duplicación de código (34.9%) sugiere que hay mucho código repetido. Esto puede llevar a problemas de mantenibilidad y aumentar el esfuerzo necesario para realizar cambios en el futuro. Se recomienda refactorizar el código para eliminar duplicaciones.

6. **Puntos Calientes de Seguridad:**
   - No hay puntos calientes de seguridad, lo cual es positivo. Sin embargo, es importante seguir monitorizando y realizando auditorías de seguridad regularmente.

## Recomendaciones

1. **Abordar Problemas de Fiabilidad y Mantenibilidad:**
   - Priorizar la resolución de los problemas abiertos, especialmente los mayores y críticos. Refactorizar el código para mejorar su estructura y mantenibilidad.

2. **Mejorar la Cobertura de Código:**
   - Implementar y ejecutar pruebas unitarias y de integración para aumentar la cobertura del código. Esto ayudará a detectar y corregir errores de manera temprana.

3. **Reducir la Duplicación de Código:**
   - Identificar y eliminar el código duplicado mediante refactorización. Utilizar patrones de diseño y funciones reutilizables para mejorar la eficiencia y la mantenibilidad.

4. **Monitorear y Auditar Regularmente:**
   - Continuar monitoreando el proyecto y realizar auditorías de seguridad periódicas para mantener la calidad del código.

Siguiendo estas recomendaciones, podrás mejorar significativamente la calidad y la mantenibilidad de tu proyecto.
