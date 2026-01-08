```mermaid
graph TD
  Start((Inicio)) --> AuthOB["Autenticación API OnBase\n(Obtener Token)"]
  AuthOB --> GetCases["Descargar Casos para Transacciones"]
  
  GetCases --> LoopCase{Por cada caso}
  
  subgraph Recoleccion ["1. Recolección y Limpieza"]
    LoopCase --> DownloadOB["Descargar documentos iniciales OnBase"]
    DownloadOB --> AuthALFA["Autenticación API ALFA\n(Obtener Token)"]
    AuthALFA --> CheckMore["Consultar documentos adicionales\nen OnBase y ALFA"]
    CheckMore --> Deduplicate["Eliminar documentos repetidos"]
  end

  subgraph Unificacion ["2. Unificación Documental"]
    Deduplicate --> GetEmail["Descargar Tapa de Correo Electrónico"]
    GetEmail --> MergePDF["Unificar PDF\nTapa + Documentos"]
  end

  subgraph Clasificacion ["3. Clasificación y Carga"]
    MergePDF --> ClassifyRules["Aplicar Reglas Parametrizadas\nde Clasificación"]
    ClassifyRules --> CheckClass{"¿Error en\nClasificación?"}
    CheckClass -- No --> UploadOB["Subir PDF a OnBase\nKeywords / Body"]
    CheckClass -- Sí --> Manual["Escalar a Analista Manual"]
  end

  subgraph Cierre ["4. Cierre y Asignación"]
    UploadOB --> CloseOB["Cerrar Caso en OnBase\n(Requiere Token)"]
    CloseOB --> CloseALFA["Cerrar Caso en ALFA\n(Requiere Token)"]
    CloseALFA --> TypifyRules["Aplicar Reglas de Tipificación\n(Archivo Externo)"]
    TypifyRules --> CheckTyp{"¿Error en\nTipificación?"}
    CheckTyp -- No --> AssignMed["Asignar a Servicios\nÁrea Médicos"]
    CheckTyp -- Sí --> Manual
  end

  AssignMed --> End((Fin))
  Manual --> End



