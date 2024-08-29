# README

# manage_permission.sh

### Inicio de tudo

#/bin/bash
### Solicita nome do projeto
echo "Nome do Projeto: "
read projeto

### Verifica se projeto já existe

if

    getent group "$projeto" > /dev/null; then 
    echo "O projeto $projeto é existente"

else
    
    echo "O projeto $projeto não existe. Criando projeto..."
    sudo groupadd "$projeto"
    echo "projeto $projeto criado.
    
### Cria um diretório para armazenar o grupo

    sudo mkdir -p "/home/shared/$projeto"
    echo "Diretório para o projeto $projeto criado em /home/shared/$projeto."
fi

    echo "Nome do Usuario: "
    read usuario

### Verifica se o usuário existe

if    

    id "$usuario" &>/dev/null; then
    echo "Usuario $usuario existe no $projeto"

else

### Verifica se o usuário já está no projeto

    if id -nG "$usuario" | grep -qw "$projeto"; then
    echo "Usuário $usuario não já está no grupo projeto."
    else
    
### Adiciona o usuário ao projeto
    sudo usermod -aG "$projeto" "$usuario"
    echo "Usuário $usuario adicionado ao projeto $projeto com sucesso."
    sudo chmod 774 /home/$usuario
    fi
    
### o chmod está dando permissão total para o dono do arquivo e usuários do grupo e apenas permissão de leitura para os demais usuários 
### edite de acordo com suas necessidades

# daily_report.sh

### Define o diretório de origem e o diretório de destino para o relatório
    shared_dir="/home/shared"
    report_dir="/var/reports"

### Cria o diretório de relatórios se não existir
    sudo mkdir -p "$report_dir"

### Define o nome do arquivo de relatório com a data atual
    report_file="$report_dir/report_$(date +%Y-%m-%d).txt"

### Gera o relatório com a lista de arquivos modificados ou criados nas últimas 24 horas
    echo "Relatório de arquivos modificados ou criados nas últimas 24 horas em $shared_dir" > "$report_file"
    echo "Data de geração: $(date)" >> "$report_file"
    echo "------------------------------------------------------" >> "$report_file"

### Encontra todos os arquivos criados ou modificados nas últimas 24 horas e grava no relatório
    find "$shared_dir" -type f -mtime -1 -print0 | while IFS= read -r -d '' file; do
    # Extrai o nome do subdiretório do projeto
    project_name=$(basename "$(dirname "$file")")
    
### Adiciona a linha de informações ao relatório
    echo "Arquivo: $file" >> "$report_file"
    echo "Projeto: $project_name" >> "$report_file"
    echo "------------------------------------------------------" >> "$report_file"

### Fim do script

    done
    echo "Relatório salvo em: $report_file"



