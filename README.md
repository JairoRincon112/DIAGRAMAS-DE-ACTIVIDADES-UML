# **DIAGRAMAS DE ACTIVIDADES UML**

**Autores:**  
Jairo Andrés Rincón Blanco  
Andres Camilo Cuvides Ortega  

---

## **Objetivos**

Diseñar y documentar un Diagrama de Actividades UML que represente el proceso de inscripción a cursos intersemestrales en la FESC tanto en su estado actual (AS-IS) como en una propuesta mejorada (TO-BE), empleando correctamente elementos avanzados del modelado como swimlanes, decisiones, forks/joins, señales, nodos de objeto y pins de entrada/salida, con el fin de analizar, comprender y optimizar el flujo operativo institucional.

---

## **Resumen**

El objetivo fue modelar el proceso actual (AS-IS) de inscripción intersemestral y proponer una versión optimizada (TO-BE) que reduce pasos manuales, paraleliza validaciones y mejora la experiencia del estudiante. El modelo TO-BE automatiza validaciones de prerrequisitos y asignación de cupos, integra pagos en línea con notificaciones automáticas y centraliza el registro en facturación.

---

## **Actores y Responsabilidades (Swimlanes)**

En el proceso de inscripción participan varios actores con responsabilidades claramente definidas: el Estudiante, quien inicia la inscripción, selecciona las materias y elige el método de pago; el Sistema Académico, encargado de validar los prerrequisitos, verificar los cupos disponibles y registrar las inscripciones; la Coordinación Académica, responsable de aprobar casos especiales y gestionar excesos de cupo; el Docente, que confirma los participantes y actualiza el syllabus; y finalmente Tesorería, que procesa los pagos, genera los recibos y valida los descuentos aplicables.

---

## **Diagramas PlantUML**

### **AS-IS (Proceso Actual)**
```plantuml
@startuml AS_IS_Inscripcion
|Estudiante|
start
:Iniciar sesión / Autenticarse;
:Solicitar inscripción intersemestral;
split
  |Sistema Académico|
  :Verificar estado académico;
  :Consultar prerrequisitos por materia;
  if (Cumple prerrequisitos?) then (sí)
    :Marcar materia permitida;
  else (no)
    :Notificar restricción al estudiante;
    detach
  endif
split again
  |Sistema Académico|
  :Verificar disponibilidad de cupos;
  if (Cupo disponible?) then (sí)
    :Reservar espacio temporalmente;
  else (no)
    |Coordinación Académica|
    :Derivar solicitud exceso de cupo;
  endif
end split
|Estudiante|
:Seleccionar materias finales;
|Sistema Académico|
:Calcular valor de matrícula intersemestral;
|Tesorería|
:Aplicar descuentos según política;
if (Pago en línea?) then (sí)
  :Procesar pago vía gateway;
else (presencial)
  :Registrar pago presencial;
endif
if (Pago exitoso?) then (sí)
  :Generar comprobante de inscripción;
  -> Comprobante : [Object]
  :Registrar en sistema de facturación;
  |Sistema Académico|
  :Activar acceso al aula virtual;
  |Docente|
  :Confirmar participantes / Actualizar syllabus;
else (no)
  :Notificar error de pago al estudiante;
endif
stop
@enduml


@startuml TO_BE_Inscripcion_Optimizado
|Estudiante|
start
:Iniciar sesión;
:Solicitar inscripción intersemestral;
|Sistema Académico|
split
  :Auto-validar estado académico (automatizado);
  :Auto-consultar prerrequisitos por materia;
split again
  :Auto-consultar disponibilidad de cupos (caché / API);
end split
join
if (Prerrequisitos OK & Cupo OK?) then (sí)
  |Sistema Académico|
  :Reservar cupo automáticamente (hold);
  :Calcular valor de matrícula + aplicar descuentos automáticos;
  |Estudiante|
  :Confirmar selección y pagar (única pantalla);
  |Tesorería|
  :Procesar pago en línea (integrado, 3DS / gateway);
  if (Pago OK?) then (sí)
    :Generar comprobante y póliza fiscal [Object Node];
    :Registrar en facturación (API);
    |Sistema Académico|
    :Activar acceso al aula virtual (auto-provision);
    |Docente|
    :Recibir notificación y confirmar si aplica;
  else (no)
    :Reintentar pago / Ofrecer conciliación presencial;
  endif
else (no)
  |Sistema Académico|
  :Ofrecer alternativas (espera, prereq, asesoría en línea);
endif
stop
@enduml
