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
### **GetAtt**
  + Devuelve el valor de un atributo de un recurso en el template
  + Se compone del nombre del recurso y del atributo
  + Sintaxis version 1
    + `{"Fn::GetAtt" : ["LogicalNameOfResource", "attributeName"]}`
  + Sintaxis version 2
    + `Fn::GetAtt : ["LogicalNameOfResource", "attributeName"]`
  + Sintaxis version 3
    + `!GetAtt LogicalNameOfResource.attributeName`
  + Cuando usar GetAtt; cuando se quiere tomar algún atributo de un recurso dentro del mismo stack.
### **FindInMap**
  + Devuelve el valor correspondiente al map declarado en la sección Mappings.
  + MapName, TopLevelKey y SecondLevelKey
  + Sintaxis version 1
    + `{"Fn::FindInMap" : [ "MapName", "TopLevelKey", "SecondLevelKey" ]}`
  + Sintaxis version 2
    + `Fn::FindInMap: [ MapName, TopLevelKey, SecondLevelKey ]`
  + Sintaxis version 3
    + `!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]`
  + Cuándo usar FindInMap; Cuando necesitamos en el template algún valor de la sección Mappings.
### **Join**
  + Une un listado de valores en uno solo
  + Se compone de un listado de valores y un limitador
  + Sintaxis version 1
    + `{"Fn::Join" : [ "delimiter", [list of values] ]}`
  + Sintaxis version 2
    + `Fn::Join : [ delimiter, [list of values] ]`
  + Sintaxis version 3
    + `!Join [delimiter, [list of values] ]`
  + Cuándo usar Join?; `"a:b:c" !Join[":"[a,b,c]] [a,b,c]`
### **Split**
  + Dividir una cadena en una lista de valores
  + Sintaxis version 1
    + `{"Fn::Split" : ["delimeter","source string"]}`
  + Sintaxis version 2
    + `Fn::Split [delimeter, source string]`
  + Sintaxis version 3
    + `!Split [delimeter, source string]`
### **Select**
  + Selecciona un valor de una lista de valores.
  + Sintaxis version 1
    + `{"Fn::Select" : [ index, listOfObjects ]}`
  + Sintaxis version 2
    + `Fn::Select: [ index, listOfObjects ]`
  + Sintaxis version 3
    + `!Select [ index, listOfObjects ]`
  + Cuándo usar Split/Select
    + Cuando necesitemos dividir un arreglo en valores independientes y tomar solo uno de esos valores.
### **Sub**
  + Sustituye una variable con un input que nosotros especifiquemos
  + Se construye de la siguiente manera: `String VarName:ValueName`
  + Sintaxis version 1
    + `{"Fn::Sub" : [ String, { Var1Name:Var1Value, Var2Name:Var2Value } ]}`
  + Sintaxis version 2
    + ```JSON
      Fn::Sub:
    - String 
    - { Var1Name: Var1Value, Var2Name: Var2Value }```
  + Sintaxis version 3
    + ```xml
      !Sub
    - String
    - { Var1Name: Var1Value, Var2Name: Var2Value }```
  + Cuándo usar Sub?; Cuando se requiera reemplazar un valor en un String, puede ser un pseudo parameter
    + `AWS::AccountId` Número de la cuenta de AWS.
    + `AWS::NotificationARNs` Arns del stack actual.
    + `AWS::NoValue` Remueve un valor de la propiedad.
    + `AWS::Partition` Devuelve la partición de un recurso.
    + `AWS::Region` Devuelve la región actual del stack
    + `AWS::StackName` Devuelve el nombre del stack
    + `AWS::StackId` Devuelve el Id del stack
    + `AWS::URLSuffix` Devuelve el sufijo de un dominio
    + Ejemplo:
      + `!Sub 'arn:aws:ec2${AWS::Region}${AWS::AccountID}:vpc${vpc}'`
### **Ref**
  + Retorna un valr de un parámetro o un recurso
  + La sintaxis es: `Stringm VarNam:ValueName`
  + Sintaxis version 1
    + `{"Ref" : "logicalName" } ]}`
  + Sintaxis version 2
    + `Ref: logicalName`
  + Sintaxis version 3
    + !Ref logicalName
  + Cuando ocupar *Ref*
    + Cuando se necesite hacer referencia a un parametro
    + Cuando queramos hacer referencia a una propiedad de un recurso que no este en GetAtt
### **Import Value**
  + Devuelve el valor de una salida exportada de otro stack
  + Referencia al nombre lógico del recurso exportado
  + Sintaxis version 1
    + `{"Fn::ImportValue" : sharedValueToImport } ]}`
  + Sintaxis version 2
    + `Fn::ImportValue: sharedValueToImport`
  + Sintaxis version 3
    + !ImportValue sharedValueToImport
  + Cuando utilizarlo; se llama a un *Export* de otro stack. Cuando queremos llamara a una propiedad exportada de otro stack

## Funciones Condicionales

###  IF
  + Retorna un valor si una condicion se cumple y otra si no se cumple
  + Se compone de la siguiente manera: `!if[condition_name, value_if_true, value_if_false]`
### OR
  + Returna true si un valor es true o false si un valor es falso
  + Se compone de la siguiente manera: `!Or[ Condition,... ]`
### AND
  + Retorna true si todos los valores son true o false si algún valor es falso
  + Se compone de la siguiente manera: `!And[condition]`
### EQUALS
  + Retuorna true si 2 valores son giuale o false si no lo son.
  + `!Equals [value1, value2]`
  
## Importancia de la automatización de infraestructura
- Despliegue en tiempos cortos
- Integridad de la infraestructura
- Pipelines seguros sin exponer datos sensibles
- Reutilización de componentes.
- Trazbilidad en todos los despliegues
- Rollback automático ante errores.


## Como automatizar despliegues de infraestructura
- **Codecommit**; Trazabilidad en todos los despliegues; es el Github de AWS.
- **Cloudformation**; infraestructura como código.
- **Codepipeline**; orquesta todos los componentes de despliegue.
- **Codebuild**; compilación y creación de artefactos.
- **Github**; Repositorio del código.
- **IAM**; Gestión de roles en el pipeline.
- **Cloudwatch**; monitoreo de todos los despliegues.
- **S3**; Almacenar los artefactos.
- **SecretManager** Gestión de secretos.
- **KMS**; llaves de seguridad en el pipeline.

##

