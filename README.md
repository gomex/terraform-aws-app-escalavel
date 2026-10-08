# terraform-aws-app-escalavel

Módulo Terraform que sobe uma aplicação escalável na AWS: um **Auto Scaling Group**
atrás de um **Application Load Balancer**, com HTTPS (**ACM**) e domínio no **Route53**.

Ele junta módulos prontos do [`terraform-aws-modules`](https://github.com/terraform-aws-modules):
`vpc`, `acm`, `alb`, `security-group`, `autoscaling` e `route53`.

Usado na demo [gomex/terraform-ao-vivo](https://github.com/gomex/terraform-ao-vivo).

```
Route53 → ACM (HTTPS) → Load Balancer → Auto Scaling Group → EC2 (2 zonas)
```

## Uso

Sempre aponte para uma **tag**, nunca para uma branch: assim uma mudança no
módulo só chega à sua infra quando você trocar a versão, num PR, com plan.

```hcl
module "app" {
  source = "git::https://github.com/gomex/terraform-aws-app-escalavel.git?ref=v1.0.0"

  nome       = "minha-app"
  dominio    = "exemplo.com.br"
  subdominio = "demo"

  quantidade_de_maquinas = 2
}
```

## Pré-requisitos

- Uma **zona do Route53** para `dominio` já existente na conta.
- Uma **AMI** na conta com nome começando por `<nome>-`. O módulo usa a mais recente.
  A AMI deve servir HTTP na porta 80, com o health check em `/saude`.

## Entradas

| Nome | Descrição | Tipo | Padrão |
|------|-----------|------|--------|
| `nome` | Nome usado nos recursos e prefixo da AMI procurada | `string` | — |
| `dominio` | Zona do Route53 existente | `string` | — |
| `subdominio` | Subdomínio que aponta para o load balancer | `string` | — |
| `quantidade_de_maquinas` | Capacidade desejada do Auto Scaling Group | `number` | — |
| `minimo_de_maquinas` | Mínimo do Auto Scaling Group | `number` | `1` |
| `maximo_de_maquinas` | Máximo do Auto Scaling Group | `number` | `10` |
| `tipo_instancia` | Tipo das instâncias EC2 | `string` | `"t3.micro"` |

## Saídas

| Nome | Descrição |
|------|-----------|
| `url` | URL HTTPS da aplicação |
| `alb_dns` | DNS do load balancer |
| `ami` | Nome e ID da AMI em uso |
| `asg` | Nome do Auto Scaling Group |

## Versões

Segue [SemVer](https://semver.org/lang/pt-BR/). Mudanças que recriam recursos
ou alteram entradas sobem a versão maior.
