# 🏋️‍♂️ GymFlow - Smart SaaS Architecture

> Ecossistema B2B2C para academias focado em roteamento inteligente de filas, gamificação estrita e eliminação de atrito operacional.

[![Status](https://img.shields.io/badge/Status-MVP%20Em%20Desenvolvimento-orange.svg)]()
[![Backend](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen.svg)]()
[![Mobile](https://img.shields.io/badge/Flutter-3.x-blue.svg)]()
[![Database](https://img.shields.io/badge/SQL%20Server-Enterprise-red.svg)]()

## 📖 Visão Geral

O **GymFlow** é uma plataforma SaaS Multi-Tenant projetada para transformar a gestão de treinos e a experiência física dentro de pequenas e médias academias. Através de um aplicativo móvel e um motor de regras de negócio robusto, o ecossistema automatiza a criação de fichas, gerencia filas virtuais de equipamentos em tempo real e aplica gatilhos psicológicos de gamificação (Streaks e Leaderboards) para maximizar a retenção de alunos.

## 🏗 Arquitetura e Stack Tecnológica

O projeto adota uma arquitetura Cliente-Servidor fortemente desacoplada, orientada a eventos e otimizada para alta concorrência.

| Componente | Tecnologia | Propósito |
| :--- | :--- | :--- |
| **Mobile App (Client)** | Flutter (Dart) | Interface do usuário, cálculo de Geofencing, UX/UI e degradação graciosa. |
| **API Server (Backend)** | Java 17 + Spring Boot 3 | Regras de negócio, roteamento de microfluxo, validação de segurança e JWT. |
| **Database (Relacional)** | MS SQL Server | Persistência transacional multi-tenant com otimização estrutural (UUIDs sequenciais). |
| **Memory / Cache** | Redis | Gerenciamento estrito de *Rate Limiting* (via Bucket4j) para proteção das APIs. |
| **Mensageria** | Firebase Cloud Messaging | Notificações push ativas (alertas de liberação de máquina e risco de perda de ofensiva). |

## ✨ Principais Motores de Negócio

* **Isolamento Multi-Tenant:** Filtragem por Inquilino (Academia) aplicada de forma global na camada ORM (Hibernate) para minimizar risco de acesso indevido entre tenants.
* **Prova de Presença por Geofencing:** O check-in e o acesso à *Fila Virtual Inteligente* exigem validação de GPS em background. Sessões sem verificação geográfica sofrem degradação graciosa: o usuário mantém seu histórico privado, mas é bloqueado do Leaderboard.
* **Fila Virtual Transparente:** O algoritmo de roteamento não usa numeração estática. A fila é uma representação temporal da disputa por uma máquina, reordenada passivamente sem sobrecarregar o banco de dados.
* **Fail-Safe de Checkout Duplo:** Checkouts são disparados via evento de quebra da cerca virtual (Mobile) e garantidos por um *Job* passivo de inatividade (Backend) rodando a cada minuto.
* **Punição e Gamificação (Streaks):** Rotinas noturnas cruzam a agenda do aluno com o histórico de conclusão. Ausências em dias planejados zeram instantaneamente a ofensiva, a menos que o usuário possua um "Congelamento" (Streak Freeze) disponível.

## 📂 Estrutura do Repositório

    .
    └── README.md                 # Documentação principal

> Nota: a estrutura de monorepo com `gymflow-backend/` e `gymflow-frontend/` ainda não está presente neste repositório. Atualize esta seção quando os módulos forem adicionados.

## 🚀 Como Iniciar o Ambiente (Getting Started)

### Pré-requisitos
* [JDK 17+](https://adoptium.net/)
* [Flutter SDK 3.0+](https://flutter.dev/docs/get-started/install)
* [Docker](https://www.docker.com/) (Para subir o SQL Server e o Redis)

### 1. Subindo a Infraestrutura
Na raiz do diretório `gymflow-backend`, suba os containers de banco de dados e cache:

    cd gymflow-backend
    docker-compose up -d

### 2. Inicializando o Backend (Spring Boot)

```bash
cd gymflow-backend
./mvnw spring-boot:run
```
A API estará disponível em `http://localhost:8080`.

### 3. Inicializando o Frontend (Flutter)

```bash
cd gymflow-frontend
flutter pub get
flutter run
```

## 🛡 Segurança e Conformidade
* **Auditoria e LGPD:** Todas as exclusões de registros (Alunos, Máquinas, Filiais) são tratadas com *Soft Delete* (`ativo = false`), garantindo rastreabilidade.
* **Idempotência:** Rotas críticas exigem chaves únicas de transação para evitar duplicação de pontuação devido a oscilações de rede.

---
**B&T Tech** © 2026
*Engenharia de Software e Consultoria Tecnológica*
*Desenvolvido por Pedro Henrique & Diogo Teodoro*
