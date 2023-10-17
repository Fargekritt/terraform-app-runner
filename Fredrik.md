# Mål

## Docker
 * [ ] Lage docker image av Spring-boot prosjektet
   * [ ] Med tag "latest" og version som matcher git commit
   * [ ] Push image til ECR (AWS sin docker hub)

## Terraform/AppRunner
* [ ] Brukes til å sette opp tingene som skal være på AWS (lage servere osv) 
      Samme som vi gjør i nettsiden men ved programmering istedet for GUI

## Github Actions
* [ ] 2 stykker actions. En kjører terraform og andre bygger docker image

# Repetisjon

## Terraform
Du beskriver hvordan infrastrukuren skal se ut når den er ferdig

```
resource "aws_apprunner_service" "service" {
    service_name = var.prefix
    source_configuration {

        authentication_configuration {
          access_role_arn = "arn:aws:iam::244530008913:role/service-role/AppRunnerECRAccessRole"
        }
    
        image_repository {
          image_configuration {
            port = "8080"
          }
          image_identifier      = var.image
          image_repository_type = "ECR"
        }
        auto_deployments_enabled = true
    }
}
```

* Vi skal ha en "resource" som er "aws_apprunner_service" og heter "service"
* Vi skal configurer source og den skal ha en authenticantion config som setter acces_role_arn (rettigheter gruppe)

### State
* State sier hva terraform har gjort så langt.
* Hvilken ting den har laget/fjernet osv
* Den spør ikke AWS hva som er. den må huske selv
* Dette er en fil som vi må holde styr på (lokalt eller skyen bestemmer vi selv) default er lokal lagring
* Siden Github Actions ikke kan lagre filer etter en kjørt action på en fornuftig måte lagrer vi den i en S3 bucket (sky lagring)
  * Dette gjør vi med å lage backend i Terraform (ikke backend som vi er vant til)
  ```
  backend "s3" {
    bucket = "pgr301-2021-terraform-state"
    key    = "glenn.richard.bech/apprunner-a-new-state.state"
    region = "eu-north-1"
  }
  ```
