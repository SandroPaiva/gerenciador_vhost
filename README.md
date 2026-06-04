# Pilkington - Sistema de Vistoria Veicular V3

## Visão Geral
Sistema robusto, responsivo e eficiente desenvolvido para otimizar as operações de reparo e troca de vidros veiculares da Pilkington. O aplicativo web foi totalmente desenhado para máxima produtividade em campo, contando com ferramentas nativas avançadas como captura de câmera, compressão de imagens no lado cliente, geolocalização e assinatura digital.

---

## 🛠 Tecnologias e Recursos Utilizados

O projeto prioriza a alta performance e controle total do código, abstendo-se de frameworks pesados:

*   **PHP 8+ (Vanilla MVC):** Arquitetura Model-View-Controller criada do zero. Responsável pelo roteamento (`Router.php`), segurança e regras de negócio.
*   **MySQL & PDO:** Banco de dados relacional acessado de forma segura (prevenindo SQL Injection) para armazenamento da inteligência do negócio.
*   **JavaScript (Vanilla):** Responsável por toda a interatividade de front-end. Utilizado para a compressão extrema de imagens no celular, geolocalização via API do navegador e renderização do quadro de Assinatura Digital via HTML5 `<canvas>`.
*   **CSS3 (Design System Corporativo):** Todo o design foi construído com CSS puro (Variáveis nativas), seguindo estritamente o Brand Book oficial da Pilkington (Vermelho `#CC0000`, Cinza Carvão `#1A1A1A`, tipografia corporativa e botões com cantos rígidos).
*   **FontAwesome (CDN):** Biblioteca visual utilizada para a renderização de ícones padronizados (menus, botões e ações).

---

## 📦 Módulos do Sistema (Para que servem)

1.  **Módulo de Autenticação (`AuthController`)**
    *   *Função:* Gerenciar o login seguro utilizando hashes `BCRYPT` e controlar a sessão do usuário no sistema.
2.  **Dashboard (`DashboardController`)**
    *   *Função:* Tela inicial que compila indicadores de desempenho (KPIs) em tempo real, informando quantas vistorias estão pendentes ou concluídas.
3.  **Gestão de Usuários (`UsuarioController`)**
    *   *Função:* Permite a administradores e gerentes cadastrar e controlar a hierarquia de operadores (Supervisores e Técnicos).
4.  **Gestão de Itens de Reparo (`ItemController`)**
    *   *Função:* Catálogo base onde a empresa define os serviços oferecidos (ex: Troca de Para-brisa, Reparo de rachadura em vigia, etc.).
5.  **Gestão de Veículos (`VeiculoController`)**
    *   *Função:* Cadastro central de clientes/veículos (Placa, Chassi) a serem atendidos, atrelando-os previamente a um Item de Reparo.
6.  **Execução de Vistoria (`VistoriaController`)**
    *   *Função:* O "coração" do sistema. É a interface mobile do Técnico de campo. Engloba o upload contínuo de fotos, captura de localização de onde o serviço está sendo feito, e coleta de assinatura do cliente.
7.  **Relatórios e PDF (`VistoriaController@pdf`)**
    *   *Função:* Renderiza o dossiê final de inspeção em formato A4 (`@media print`), gerando PDFs de forma nativa e rápida.

---

## 🐛 Histórico de Problemas Encontrados e Soluções (Troubleshooting)

Durante o desenvolvimento do MVP, alguns bloqueios importantes foram superados:

*   **Problema 1: Layout Genérico fora de Padrão**
    *   *Ocorrido:* O design inicial não condizia com a seriedade e cores da marca Pilkington.
    *   *Correção:* Reescrevemos o `style.css` baseando-se no documento `layout.md`, removendo painéis translúcidos, alterando a cor primária de Azul para o Vermelho Pilkington e usando geometria sólida.
*   **Problema 2: Falha no Login de Administrador**
    *   *Ocorrido:* O usuário `admin` não acessava o sistema. O script de banco de dados tinha gerado um hash incompatível para a senha `admin123`.
    *   *Correção:* O hash correto foi gerado no terminal usando o motor `BCRYPT` padrão do PHP 8, e o banco (além do `.sql`) foi atualizado via comando MySQL.
*   **Problema 3: Vistorias não Salvavam Fotos (Silenciamento PHP)**
    *   *Ocorrido:* O usuário clicava em "Concluir Vistoria" após inserir várias fotos do celular, porém nada acontecia e o sistema perdia tudo, voltando para "Em andamento". O limite global de upload do servidor (`post_max_size`) de 8MB estava sendo estourado por fotos de celular de 5MB cada.
    *   *Correção:* O problema foi resolvido em duas frentes. Primeiro, no Servidor, o limite foi aumentado para 128MB. Segundo, no Aplicativo, **construímos um Motor de Compressão JS** que pega as fotos originais e as reduz/comprime para até 1280px (tamanho de WhatsApp) diretamente no celular do técnico antes de enviá-las para a rede, salvando tempo e 4G.
*   **Problema 4: Câmera não Abrindo Nativa e Imagens não Salvas no PDF**
    *   *Ocorrido:* Os botões limitavam o uso da galeria, e o PDF gerava as imagens quebradas por falta da pasta.
    *   *Correção:* (1) A tag `<button>` foi substituída por `<label>` conectada aos inputs nativos, o que libera o Popup OS de escolha de Câmera/Galeria para o Android/iOS. (2) Via terminal, a pasta `/public/assets/img/` foi criada e teve as permissões abertas `chmod 777` liberadas, permitindo que o sistema converta a imagem Base64 do JS em arquivos JPG definitivos.

---

## 🚀 Passo a Passo: Como Usar o Sistema

Siga estas instruções para garantir a operação diária correta:

### Passo 1: Cadastro Inicial (Base de Dados)
1.  **Acesse o sistema** usando seu usuário e senha.
2.  No menu lateral, vá em **Itens de Reparo**. Cadastre os vidros ou serviços que serão prestados (ex: Troca de Parabrisa, Farol).
3.  Vá em **Veículos** e clique em "Novo Veículo". Registre a Placa do cliente e conecte ao Item de Reparo recém-criado.

### Passo 2: Delegação da Vistoria
1.  Vá no menu **Vistorias** e clique em **Iniciar Nova Vistoria**.
2.  Selecione o Veículo que você acabou de cadastrar.
3.  Delegue ao "Técnico" responsável. O sistema registrará automaticamente o status como `Em Andamento`.

### Passo 3: Execução do Serviço em Campo (Técnico)
1.  O técnico acessa o menu **Vistorias** pelo Celular/Tablet.
2.  Na Vistoria designada a ele, ele clica no botão Azul **"Continuar Reparo"**.
3.  **Fotos:** O técnico usa o botão de "Adicionar Fotos". Ele tira a 1ª foto. Clica de novo e tira a 2ª, até completar no mínimo 5 e no máximo 10 nas sessões Pré e Pós-Reparo. As miniaturas aparecem na tela de imediato.
4.  **Localização:** Ele clica no botão "Capturar Localização Atual". O celular pedirá permissão e gravará o GPS da oficina/campo.
5.  **Assinatura:** Ele clica em "Coletar Assinatura". O aparelho é girado de lado, o cliente assina no quadro em branco e clica em Salvar.
6.  Preenche a descrição do trabalho e clica em **Concluir Vistoria**. (A tela de carregamento aparecerá enquanto comprime as imagens).

### Passo 4: Impressão do Relatório
1.  Após a conclusão, um botão verde **"Download PDF"** aparecerá ao lado da Vistoria na lista.
2.  Ao clicar, a página A4 oficial corporativa será montada organizando todas as imagens. A caixa de diálogo de impressão do navegador será disparada.
3.  Basta selecionar "Salvar como PDF" ou imprimir na impressora da loja e entregar ao cliente.
