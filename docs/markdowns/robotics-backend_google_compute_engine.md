# Despliegue de Robotics-Backend en Google Compute Engine

## Pasos:
- **1.** Crear una nueva instancia de Maquina Virtual.
- **2.** Rellenar los campos necesarios que salen por pantalla.

## Estimación de coste mensual segun configuraciones predeterminadas y zona: 

Región: europe-southwest1 (Madrid), zona cualquiera

    E2 (Procesamiento diario de bajo costo), Plataforma Intel Broadwell:

        E2-standard-4 (2 vCPU, 2 núcleos, 16 GB RAM):
        115.92 USD/mes; 0.161 USD/hora

        e2-standard-8 (4 CPU virtuales, 4 núcleos, 32GB RAM):
        231.37 USD/mes; 0.321 USD/hora
    
    N1 (Precio y rendimiento equilibrados), Plataforma Intel Haswell:
        N1-standard-4 (4 vCPU, 2 núcleos, 15 GB RAM):
        107.20 USD/mes; 0.149 USD/hora

    N2D (Precio y rendimiento equilibrados), Plataforma ADM Milan:
        n2d-standard-8 (8 vCPU, 4 núcleos, 32 GB RAM):
        233.37 UDS/mes; 0.324 USD/hora

Región: europe-west1 (Bélgica), zona cualquiera:

    E2, Plataforma Intel Broadwell:

        e2-standard-4 (2 vCPU, 2 núcleos, 16 GB RAM):
        108.62 USD/mes (-7.3 USD/mes respecto a Madrid); 0.151 USD/hora

        e2-standard-8 (4 vCPU, 4 núcleos, 32GB RAM):
        216.25 USD/mes (-15.12 USD/mes respecto a Madrid); 0.3 USD/hora
    
    N1, Plataforma Intel Haswell:
        n1-standard-4 (4 vCPU, 2 núcleos, 15 GB RAM):
        107.80 USD/mes (+0.60 USD/mes respecto a Madrid); 0.15 USD/hora

    N2D, Plataforma ADM Milan:
        n2d-standard-8 (8 vCPU, 4 núcleos, 32 GB RAM):
        218.12 UDS/mes (-15.25 USD/mes respecto a Madrid); 0.303 USD/hora

> [!IMPORTANT]  
> **Detener la instancia cuando no se esté usando para disminuir costes.**


# Despliegue de Robotics-Backend en AWS con EC2

## Pasos:
- **1.** Seleccionar instancia EC2.
- **2.** Rellenar los campos necesarios que salen por pantalla.

## Precio estimado

Región: Madrid (eu-south-1)

    Instancia:
        t3.large (4 CPU virtual, 16 GB de memoria):
        121.50 USD/mes aprox; 0.169 USD/hora
        
        m5.xlarge (4 CPU virtual, 16 GB de memoria):
        160 USD/mes aprox; 0.222 USD/hora

> [!IMPORTANT]  
> **Detener la instancia cuando no se esté usando para disminuir costes.**