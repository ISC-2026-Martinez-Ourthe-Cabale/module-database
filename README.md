# 🗄️ module-database

**Repositorio:** `ISC-2026-Martinez-Ourthe-Cabale/module-database`
**Lenguaje:** HCL (Terraform)

## Descripción

Este módulo aprovisiona una instancia de Amazon RDS con MySQL 8.0 desplegada en subnets privadas. Además, configura un DB Subnet Group para soportar despliegues Multi-AZ, habilita el cifrado en reposo, backups automáticos y define ventanas de mantenimiento.

## Recursos Creados

| Recurso AWS           | Descripción                                 |
| --------------------- | ------------------------------------------- |
| `aws_db_instance`     | Instancia Amazon RDS MySQL                  |
| `aws_db_subnet_group` | Grupo de subnets privadas utilizado por RDS |

## Configuración por Defecto

| Parámetro                           | Valor por defecto |
| ----------------------------------- | ----------------- |
| Motor                               | `MySQL 8.0`       |
| Tipo de instancia                   | `db.t3.micro`     |
| Almacenamiento inicial              | `20 GB (gp3)`     |
| Almacenamiento máximo               | `100 GB`          |
| Cifrado en reposo                   | Sí                |
| Acceso público                      | No                |
| Backups automáticos                 | `7 días`          |
| Actualizaciones menores automáticas | Sí                |
| Base de datos inicial               | `ecommerce`       |

## Variables de Entrada

| Variable                  | Tipo           | Default                 | Descripción                                       |
| ------------------------- | -------------- | ----------------------- | ------------------------------------------------- |
| `name`                    | `string`       | —                       | Nombre del proyecto                               |
| `db_engine`               | `string`       | `"mysql"`               | Motor de base de datos                            |
| `db_engine_version`       | `string`       | `"8.0"`                 | Versión del motor                                 |
| `db_instance_class`       | `string`       | `"db.t3.micro"`         | Tipo de instancia RDS                             |
| `db_name`                 | `string`       | `"ecommerce"`           | Nombre inicial de la base de datos                |
| `db_username`             | `string`       | —                       | Usuario administrador                             |
| `db_password`             | `string`       | —                       | Contraseña del administrador                      |
| `db_port`                 | `number`       | `3306`                  | Puerto de la base de datos                        |
| `allocated_storage`       | `number`       | `20`                    | Almacenamiento inicial (GB)                       |
| `max_allocated_storage`   | `number`       | `100`                   | Almacenamiento máximo (GB)                        |
| `storage_type`            | `string`       | `"gp3"`                 | Tipo de almacenamiento                            |
| `multi_az`                | `bool`         | `false`                 | Habilitar despliegue Multi-AZ                     |
| `backup_retention_period` | `number`       | `7`                     | Retención de backups (días)                       |
| `backup_window`           | `string`       | `"03:00-04:00"`         | Ventana de ejecución de backups                   |
| `maintenance_window`      | `string`       | `"sun:04:00-sun:05:00"` | Ventana de mantenimiento                          |
| `skip_final_snapshot`     | `bool`         | `false`                 | Omitir snapshot final al destruir                 |
| `deletion_protection`     | `bool`         | `false`                 | Habilitar protección contra borrado               |
| `rds_security_group_id`   | `string`       | —                       | ID del Security Group asociado a RDS              |
| `private_db_subnet_ids`   | `list(string)` | —                       | IDs de las subnets privadas para la base de datos |

## Outputs

| Output                 | Descripción                                                |
| ---------------------- | ----------------------------------------------------------- |
| `db_instance_id`       | ID de la instancia RDS (usado por el módulo de monitoreo) |
| `db_endpoint`          | Endpoint de conexión (host:puerto) a la base de datos      |
| `db_address`           | Dirección/host de la base de datos, sin el puerto          |
| `db_port`              | Puerto de la base de datos                                  |
| `db_name`              | Nombre de la base de datos inicial                          |
| `db_subnet_group_name` | Nombre del DB Subnet Group creado                            |

## Ejemplo de Uso

```hcl
module "database" {
  source = "git::ssh://git@github.com/ISC-2026-Martinez-Ourthe-Cabale/module-database.git"

  name                  = "Obligatorio"
  db_username           = var.db_username
  db_password           = var.db_password
  rds_security_group_id = module.security_groups.rds_security_group_id
  private_db_subnet_ids = module.networking.private_db_subnet_ids

  # Recomendado solo para ambientes de desarrollo
  skip_final_snapshot = true
}
```
