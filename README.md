![](https://img.shields.io/static/v1?label=technology&message=AWS&color=blue)
![](https://img.shields.io/static/v1?label=technology&message=CloudFormation&color=red)
![](https://img.shields.io/static/v1?label=technology&message=DynamoDB&color=red)
![](https://img.shields.io/static/v1?label=school&message=platzi&color=green)
![](https://img.shields.io/static/v1?label=course&message=CursoDeInfraestructuraComoCódigoEnAWS&color=green)

# Infraestructura como código en AWS

## Como desplegar infraestructura en cloud.
- Existen distintas herramientas y proveedores del servicio (AWS, Azure)
- **Versionamiento**, podemos tener la infraestructura versionada, version 1 puede tener el servidor, la version 2 servidor + base de datos.
- **Trazabilidad**, quien solicitó?, quien lo aprobó?, qué cambio hizo?.
- **Eficiencia**, puedo tomar un template y utilizarlo en otros ambientes (dev,stg,prod). Esto nos permite tener automatizar, re-utilizar y homogenizar.
- **Infraestructura inmutable**, Desplegar todo de cero, no intentar arreglar. Es más eficiente.

## Herramienta para desplegar infraestructura como código.
+ **Terraform**
  + Puede hacer desplegue multi-cloud
  + Tiene version opensource y enterprise
+ **Pulumi**
  + Se utiliza el conocimiento de algún lenguaje en especifico para desplegar infraestructura.
  + Puede hacer desplegue multi-cloud
+ **Serverless Framework**
  + Despliegue de arquitectura serverless.
+ **SDK**
+ **CDK** Cloud Developer Kit, Aqui no se usan librerias particulares como en SDK.
+ **AWS SAM** Serverless Application Model
  + Herramienta orientada a aplicaciones serverless.

## Introducción y ventajas de usar CloudFormation
+ Características
  + **Templates** JSON o YAML
  + **Servicios** Stacks,Stack Set y Designer
  + **Integración** Full integración con todos los recursos de AWS.
+ Beneficios
  + AWS da soporte sobre tu código (si tienes contrato de soporte).
  + Nuevos servicios de AWS con soporte para CloudFormation.
  + Crear arquitecturas de forma gráfica.
  + Se pueden hacer despliegues multi-cuenta.
  + Crear recursos dinámicamente con custom resources
  + Gratis (CloudFormation)
  + Permite escalabilidad de recursos Multi-Región.
  + Cifrado en tránsito y REST en los recursos desplegados.
  + Es estable porque es administrado por AWS.
  + Es transaccional, el servicio espera a los recursos por crearse. Tiene Rollback.

## Anatomía de un template en CloudFormation
+ Template
  + `AWSTemplateFormatVersion: "version date"` (opcional)
  + `Description: String` (opcional)
  + `Metadata: template metadata` (opcional)
    + AWS::CloudFormation::Init
    + AWS::CloudFormation::Interface
    + AWS::CloudFormation::Designer
  + `Parameters: set of parameters` (opcional)
    + Valores que se le pasan a la plantilla cuando se crea o actualiza un stack. Pueden ser referenciados desde Resources o Outputs
  + `Mappings: set of mappings` (opcional)
    + Llave valor asociados que se usan para parámetros condicionales. Similar a una tabla de búsqueda. Utiliza la función `Fn::FindInMap`
  + `Conditions: set of conditions` (opcional)
    + Si la condición es true, entonces crea otro volúmen.
  + `Transform: set of transforms` (opcional)
    + Para aplicaciones serverless, si se especifica se pueden sar sintaxis de AWS SAM
  + `Resources: set of resources` (OBLIGATORIO)
    + Especifica los recursos y las propiedades a crear.
  + `Outputs: set of outputs` (opcional)
    + Valores devueltos de las propiedades del stack.

## Stacks: caracteristicas y despliegue
- Stack; colección de recursos que se manejan como una unidad
- CloudFormation asegura que todos los recursos sean creados o eliminados.
- Y si un recurso falla?; se hace rollback a todos los recursos del stack
- Qué pasa si borro un stack?; se borran los recursos asociados a ese stack.
- Qué es un drift (desviación)? Detecta una desviación entre el stack y los recursos desplegados.
- Qué puedo identificar con un Drift? Recursos agregados, eliminados y con propiedades diferentes.

## StackSet: despliegue multi-cuenta
- Qué tipo de cuentas existen?; Administrador y cuentas target.
- Desde dónde se despliegan recursos multi-cuenta?; Se debe hacer desde una cuenta maestra que tenga permisos sobre las otras.
- Instancia de stack; hace referencia a un staj dentro de una cuenta.
- Diferentes parámetros; se pueden desplegar stack set que cambien de parámetros dependiendo de la cuenta destino.

## Nested Stacks
+ Límites de CloudFormation
  + 100 Mappings
  + 200 Resources
  + 51,200 bytes; Cuerpo de template para crear `CreateStack`, `UpdateStack`, `ValidateTemplate`
  + 460,800 bytes; Tamaño máximo de template en S3
+ Cuando usar nested stacks
  + Para crear recursos que necesiten pasar los límites de CloudFormation
  + Cada recurso queda con un stack independiente (Granularidad)
  + Se pueden crear precedencias y condiciones entre recursos.
  + Los stacks se comunican entre sí a través de outputs.

## Funciones intrínsecas
+ **GetAtt**
  + Devuelve el valor de un atributo de un recurso en el template
  + Se compone del nombre del recurso y del atributo
  + Sintaxis version 1
    + `{"Fn::GetAtt" : ["LogicalNameOfResource", "attributeName"]}`
  + Sintaxis version 2
    + `Fn::GetAtt : ["LogicalNameOfResource", "attributeName"]`
  + Sintaxis version 3
    + `!GetAtt LogicalNameOfResource.attributeName`
  + Cuando usar GetAtt; cuando se quiere tomar algún atributo de un recurso dentro del mismo stack.
+ **FindInMap**
  + Devuelve el valor correspondiente al map declarado en la sección Mappings.
  + MapName, TopLevelKey y SecondLevelKey
  + Sintaxis version 1
    + `{"Fn::FindInMap" : [ "MapName", "TopLevelKey", "SecondLevelKey" ]}`
  + Sintaxis version 2
    + `Fn::FindInMap: [ MapName, TopLevelKey, SecondLevelKey ]`
  + Sintaxis version 3
    + `!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]`
  + Cuándo usar FindInMap; Cuando necesitamos en el template algún valor de la sección Mappings.
+ **Funcionalidad**
  + Une un listado de valores en uno solo
  + Se compone de un listado de valores y un limitador
  + Sintaxis version 1
    + `{"Fn::Join" : [ "delimiter", [list of values] ]}`
  + Sintaxis version 2
    + `Fn::Join : [ delimiter, [list of values] ]`
  + Sintaxis version 3
    + `!Join [delimiter, [list of values] ]`
