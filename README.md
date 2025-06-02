# Django + Celery: Tarefas Assíncronas com Feedback

Exemplo de integração entre Django e Celery para processamento de tarefas assíncronas, como envio de e-mails a partir de um formulário de feedback.

---

## Sumário

- [Visão Geral](#visão-geral)
- [Funcionalidades](#funcionalidades)
- [Pré-requisitos](#pré-requisitos)
- [Instalação e Execução](#instalação-e-execução)
- [Comandos Úteis](#comandos-úteis)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Referências](#referências)

---

## Visão Geral

O projeto demonstra como adicionar processamento assíncrono em aplicações Django usando o Celery, com Redis como message broker e backend de resultados. O caso de uso é o envio de e-mails de feedback, que ocorre de forma assíncrona para não bloquear o fluxo da aplicação web.

---

## Funcionalidades

- Envio assíncrono de e-mails via formulário de feedback.
- Integração Celery + Django.
- Uso do Redis como broker e backend de resultados.
- Código exemplo para criação e execução de tasks.

---

## Pré-requisitos

- Python 3.8+
- Redis instalado e executando na máquina (`redis-server`)
- (Recomendado) Ambiente virtual Python

---

## Instalação e Execução

1. **Clone o repositório**
   ```sh
   git clone https://github.com/carvalhocaio/asynchronous-tasks-with-django-and-celery.git
   cd asynchronous-tasks-with-django-and-celery
   ```

2. **Crie e ative um ambiente virtual**
   ```sh
   python -m venv .venv
   source .venv/bin/activate   # Windows: .venv\Scripts\activate
   ```

3. **Instale as dependências**
   ```sh
   pip install -r requirements.txt
   ```

4. **Inicie o Redis**
   ```sh
   redis-server
   # ou via Docker:
   # docker run -d -p 6379:6379 redis
   ```

5. **Execute as migrações do Django**
   ```sh
   python manage.py migrate
   ```

6. **Execute o servidor Django**
   ```sh
   python manage.py runserver
   ```

7. **Inicie um worker Celery**
   ```sh
   python -m celery -A django_celery worker -l info
   ```
   > O nome do projeto é `django_celery` (verifique se o parâmetro `-A` corresponde).

---

## Comandos Úteis

- Rodar worker Celery:
  ```sh
  python -m celery -A django_celery worker
  ```
- Rodar worker Celery com nível de log:
  ```sh
  python -m celery -A django_celery worker -l info
  ```

---

## Estrutura do Projeto

- `django_celery/` — Configuração principal Django + Celery.
- `feedback/` — Aplicação responsável pelo formulário e envio de feedback.
- `templates/` — Templates HTML do frontend.
- `requirements.txt` — Dependências do projeto.

---

## Referências

- [Documentação Celery](https://docs.celeryq.dev/en/stable/)
- [Documentação Django](https://docs.djangoproject.com/)
- [Celery + Django Tutorial Real Python](https://realpython.com/asynchronous-tasks-with-django-and-celery/)

---

Desenvolvido por [carvalhocaio](https://github.com/carvalhocaio)
