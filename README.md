```mermaid
graph TD
  Start((Inicio)) --> AuthOB[Autenticación API OnBase<br/>(Obtener Token)]
  AuthOB --> GetCases[Descargar Casos para Transacciones]
  
  GetCases --> LoopCase{Por cada caso}
  
  subgraph Recoleccion ["1. Recolección y Limpieza"]
    LoopCase --> DownloadOB[Descargar documentos iniciales OnBase]
    DownloadOB --> AuthALFA[Autenticación API ALFA<br/>(Obtener Token)]
    AuthALFA --> CheckMore[Consultar documentos adicionales<br/>en OnBase y ALFA]
    CheckMore --> Deduplicate[Eliminar documentos repetidos]
  end

  subgraph Unificacion ["2. Unificación Documental"]
    Deduplicate --> GetEmail[Descargar Tapa de Correo Electrónico]
    GetEmail --> MergePDF[Unificar PDF: Tapa + Documentos]
  end

  subgraph Clasificacion ["3. Clasificación y Carga"]
    MergePDF --> ClassifyRules[Aplicar Reglas Parametrizadas<br/>de Clasificación]
    ClassifyRules --> CheckClass{¿Error en<br/>Clasificación?}
    CheckClass -- No --> UploadOB[Subir PDF a OnBase<br/>(Llenado de Keywords/Body)]
    CheckClass -- Sí --> Manual[Escalar a Analista Manual]
  end

  subgraph Cierre ["4. Cierre y Asignación"]
    UploadOB --> CloseOB[Cerrar Caso en OnBase<br/>(Requiere Token)]
    CloseOB --> CloseALFA[Cerrar Caso en ALFA<br/>(Requiere Token)]
    CloseALFA --> TypifyRules[Aplicar Reglas de Tipificación<br/>(Archivo Externo)]
    TypifyRules --> CheckTyp{¿Error en<br/>Tipificación?}
    CheckTyp -- No --> AssignMed[Asignar a Servicios Área Médicos]
    CheckTyp -- Sí --> Manual
  end

  AssignMed --> End((Fin))
  Manual --> End

