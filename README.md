# 🚀 Teste Técnico de Performance - BlazeDemo

Este repositório contém a solução desenvolvida para o desafio técnico de engenharia de performance. O objetivo principal foi mapear o fluxo de compra de passagens aéreas na plataforma [BlazeDemo](https://www.blazedemo.com) e validar o comportamento e resiliência do sistema sob cenários controlados de **Carga** e **Pico (Spike Test)**.

---

## 📋 Critérios de Aceitação Exigidos

Para que o cenário fosse considerado bem-sucedido, a aplicação deveria suportar:
1. Uma vazão alvo de **250 requisições por segundo (RPS)**.
2. Um tempo de resposta no **90th percentil (90% das requisições) inferior a 2 segundos (2000 ms)**.

---

## 🛠️ Ferramentas e Tecnologias Utilizadas

* **Ferramenta de Performance:** Apache JMeter v5.6.3
* **Cenário de Negócio:** Fluxo completo e linear de compra bem-sucedida:
  1. `01_Home` (GET) - Acesso à página inicial.
  2. `02_Reserve` (POST) - Seleção das cidades de origem e destino.
  3. `03_Purchase` (POST) - Escolha do voo e preenchimento dos dados do passageiro.
  4. `04_Confirmation` (POST) - Confirmação da compra (validada via *Response Assertion* com o texto `"Thank you for your purchase today!"`).
* **Gerenciamento de Estado:** *HTTP Cookie Manager* e *HTTP Request Defaults*.

---

## 🏃 Como Executar os Scripts

### Pré-requisitos
* Ter o [Apache JMeter](https://jmeter.apache.org/) instalado localmente.

### Instruções para Execução (Modo CLI / Non-GUI)

Por boas práticas de engenharia de performance, os testes foram executados via linha de comando para evitar o consumo de recursos da interface gráfica (GUI).

1. Clone este repositório na sua máquina:
   ```bash
   git clone https://github.com/Rennan-Soares/test-performance-blazedemo.git
   cd test-performance-blazedemo

2. Para executar o Teste de Carga, utilize o seguinte comando:
    jmeter -n -t scripts/blazedemo_test.jmx -l resultados_carga.jtl -e -o ./relatorio-carga

3. Para executar o Teste de Pico, utilize o seguinte comando:
    jmeter -n -t scripts/blazedemo_spike_test.jmx -l resultados_pico.jtl -e -o ./relatorio-pico

---

### 📊 Relatório de Execução e Análise Técnica

1. Cenário A: Teste de Carga (Load Test)
Objetivo: Avaliar a estabilidade e o comportamento do sistema sob uma carga constante com rampa de subida suave.

Métricas Obtidas:

90th Percentil (90% Line): 424.00 ms 🟢 (Atingido - Limite era 2000 ms)

Throughput Médio: 194.98 transações/segundo ⚠️ (Alvo: 250 RPS)

Taxa de Erro: 0.03% (Apenas 35 falhas em 117.038 amostras)

Análise do Critério de Aceitação: Parcialmente Satisfatório. O tempo de resposta superou as expectativas, entregando menos de meio segundo de latência para 90% dos usuários virtuais. Contudo, a vazão máxima ficou retida em ~195 RPS, atingindo cerca de 78% da meta estipulada.

Motivo Técnico: Sendo o BlazeDemo um ambiente público e gratuito para treinos, a infraestrutura compartilhada possui limites de taxa (Rate Limiting) e concorrência para proteção do servidor. Pequenas flutuações e picos isolados de tempo máximo (Max: 10.6s) geraram as 35 falhas observadas, indicando o início de saturação do ambiente para manter a meta agressiva de 250 RPS.

2. Cenário B: Teste de Pico (Spike Test)
Objetivo: Avaliar a capacidade do servidor de absorver um impacto violento e repentino de tráfego (rampa de subida inferior a 10 segundos) e analisar sua recuperação.

Métricas Obtidas:

90th Percentil (90% Line): 418.00 ms 🟢 (Atingido - Limite era 2000 ms)

Throughput Médio: 240.51 transações/segundo 🟢 (Atingido - 96.2% de aderência à meta)

Taxa de Erro: 0.00% 🟢 (Nenhuma falha em 28.941 amostras)

Análise do Critério de Aceitação: Totalmente Satisfatório. Surpreendentemente, no cenário de pico a aplicação demonstrou uma resiliência impecável. A vazão média colou no teto estipulado, atingindo 240.51 RPS. O tempo de resposta permaneceu estável e controlado na casa dos 418ms e nenhum erro foi gerado, provando que o sistema lida de forma robusta com surtos repentinos de acessos em períodos de curta duração.

🗂️ Evidências do Projeto
Os arquivos e relatórios estão organizados na estrutura abaixo:

scripts/: Contém os arquivos .jmx de cada cenário configurado.

reports/: Contém imagens das tabelas estatísticas e arquivos .zip com os Dashboards interativos em HTML gerados pelo JMeter para conferência detalhada.