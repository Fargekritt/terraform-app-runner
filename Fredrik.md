# Mål
Han gjør oppgen i cloud 9 i AWS jeg gjør den på intellij på Windows
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


# Oppgaven

## Setup
* [ ] Først fork has Repo
* [ ] Om du bruker cloud 9
  * [ ] ``git clone https://github.com/≤github bruker>/terraform-app-runner.git``
  * [ ] ``git config --global credential.helper "cache --timeout=86400"`` Bare for å slippe at du må logge inn på github hele tiden
  * [ ] Logger inn på github
       ```
        git config --global user.name <github brukernavn>
        git config --global user.email <email for github bruker>
       ```
  * [ ] Når du gjør en pull så må du hente en token fra github. denne får du fra settings på profilen din
* [ ] Om du bruker intellij
  * [ ] Ha Terraform installert (Direkte i windows er OK) ``terraform --version`` i Cmd for å sjekke version 1.6.1
  * [ ] Ha AWS CLI installert ``aws`` i cmd for å sjekke
* [ ] Aktiver Github actions på repo. bare trykk på actions så kommer det opp om det ikke er i orden
* [ ] Legg til AWS secrets til i githuben
  * [ ] Gå til AWS nettsiden søk "IAM"
  * [ ] Lagg nye access keys
  * [ ] Settings på repo
  * [ ] "secrets and varibales"
  * [ ] "actions" så "new repository secret"
  * [ ] Legg til begge verdiene du får fra IAM tingen

## Terraform med lokal state fil
* [ ] bytt navn/key på s3 backend i provider.tf
* [ ] gå til terraform-app-runner/terraform-demo  ``cd terraform-demo `` om du er på intellij
* [ ] Kjør kommandoene
 ```
terraform init 
terraform plan
 ```
* [ ] avbyrt når den spør om repo
* [ ] Forandre ecr.tf til ha en default value (default = "fredik-repo") (ingen store bokstaver)
* [ ] Kjør terraform plan på nytt
  * init trenger du bare å kjøre engang
  * Den laster ned magien som faktisk kjører terraform koden din (provider)
* [ ] om du vill overstyre default varibabler ``terraform plan -var="repo_name=glennbech-mainrepo"``
* [ ] Kjør ``terraform apply`` skriv "yes"
* [ ] for å slippe å måtte skrive yes ``terraform apply --auto-approve``
* [ ] Det skal nå ligge terraform.state i terraform-demo mappen. Dette er staten som skal senere lagres i s3 bucket

## State
 * Om du sletta state filan å prøve å kjøre plan -> apply igjen så får du feilmeilding.
 * ecr-repo finnes fra før men det vet ikke terraform om.
 * Gå til AWS ECR og slett repo (ECR == dockerhub for aws)
 * Dette må ikke gjøres men kan være lurt å gjøre det for mengde trening


