```mermaid
graph TD
  Start((Inicio)) --> AuthOB[Autenticación API OnBase|Obtener Token]
  AuthOB --> GetCases[Descargar Casos para Transacciones]
  
  GetCases --> LoopCase{Por cada caso}
  
  subgraph Recoleccion ["1. Recolección y Limpieza"]
    LoopCase --> DownloadOB[Descargar documentos iniciales OnBase]
    DownloadOB --> AuthALFA[Autenticación API ALFA|Obtener Token]
    AuthALFA --> CheckMore[Consultar documentos adicionales|OnBase y ALFA]
    CheckMore --> Deduplicate[Eliminar documentos repetidos]
  end

  subgraph Unificacion ["2. Unificación Documental"]
    Deduplicate --> GetEmail[Descargar Tapa de Correo Electrónico]
    GetEmail --> MergePDF[Unificar PDF|Tapa + Documentos]
  end

  subgraph Clasificacion ["3. Clasificación y Carga"]
    MergePDF --> ClassifyRules[Aplicar Reglas Parametrizadas|de Clasificación]
    ClassifyRules --> CheckClass{¿Error en|Clasificación?}
    CheckClass -- No --> UploadOB[Subir PDF a OnBase|Keywords / Body]
    CheckClass -- Sí --> Manual[Escalar a Analista Manual]
  end

  subgraph Cierre ["4. Cierre y Asignación"]
    UploadOB --> CloseOB[Cerrar Caso en OnBase|Requiere Token]
    CloseOB --> CloseALFA[Cerrar Caso en ALFA|Requiere Token]
    CloseALFA --> TypifyRules[Aplicar Reglas de Tipificación|Archivo Externo]
    TypifyRules --> CheckTyp{¿Error en|Tipificación?}
    CheckTyp -- No --> AssignMed[Asignar a Servicios|Área Médicos]
    CheckTyp -- Sí --> Manual
  end

  AssignMed --> End((Fin))
  Manual --> End


