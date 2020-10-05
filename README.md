# i-educar-restore

Este é um script para auxiliar no download e na restauração de bases para o
sistema i-Educar. Para que ele funcione são feitas algumas suposições:

- Os backups são guardados no serviço da AWS S3;
- É feito um backup diário da base;
- O caminho do backup envolve o nome da base.

## Como instalar

Faça download do script na lista de releases:

https://github.com/eberfreitas/i-educar-restore/releases

Após ter feito o download, dê permissão de execução para o arquivo:

```
$ chmod +x restore.phar
```

E se quiser usar o script globalmente, basta movê-lo para uma pasta em seu
`path`. Ex.:

```
$ sudo mv restore.phar /usr/local/bin/restore
```

Depois disso faça uma cópia do arquivo de configurações que você encontra
[aqui](https://raw.githubusercontent.com/eberfreitas/i-educar-restore/master/config.ini.example).

Coloque numa pasta qualquer, renomeie para `config.ini` e altere os valores de
acordo com sua realidade. Da mesma pasta onde está este arquivo execute o
script:

```
$ restore -b ieducar
```

Este comando irá restaurar o banco de dados `ieducar`. Se você quiser rodar o
script de outra pasta você ainda pode informar o caminho do arquivo de
configuração assim:

```
$ restore -b ieducar -c /path/do/config.ini
```

Ou se preferir use uma configuração global colocando o arquivo em:

```
~/.config/i-educar-restore/config.ini
```

Desta forma, independentemente de onde estiver, estas configurações serão
utilizadas sem a necessidade de especificar o arquivo na linha de comando.

Ao informar o arquivo de configuração diretamente ele pode ter qualquer nome. E
se você quiser pode executar o script sem qualquer arquivo de configuração.
Basta informar todas as chaves de configuração conforme descrito abaixo:

```
$ restore --help
usage: restore [<options>]

Database restore tool for i-Educar.

OPTIONS
  --aws-bucket          AWS bucket (can be informed in the config file).
  --aws-key             AWS key (can be informed in the config file).
  --aws-prefix          AWS search prefix (can be informed in the config file).
  --aws-region          AWS prefered region (can be informed in the config
                        file).
  --aws-secret          AWS secret (can be informed in the config file).
  --base, -b            Database name to be restored. Ex.: saopaulo.
  --config-file, -c     Config file path. Defaults to "config.ini" in the
                        current directory.
  --date, -d            Backup date. Defaults to current date.
  --help, -?            Display this help.
  --host, -h            DB host (can be informed in the config file).
  --include-audit, -i   Restore with audit tables. They are not restored by
                        default.
  --newbase, -B         New database to be created. Defaults to --base.
  --password, -P        DB password (can be informed in the config file).
  --port, -p            DB port (can be informed in the config file).
  --user, -u            DB user (can be informed in the config file).
  --jobs, -j            Number of jobs used by postgres on restore

$ restore -b ieducar
```

## Configurações

Em geral as chaves da configuração são auto explicativas mas algumas
configurações tem comportamento especial:

| Chave | Descrição |
|---|---|
| date| Por padrão o script irá buscar por backups na data atual mas você pode informar alguma outra data com esta chave. Ex. `--date 2019-01-01`. Independente do formato que você usar para informar a data, internamente ela será convertida para o formato YYYY-MM-DD. |
| aws-prefix | Quando o sistema for buscar pelo arquivo de backup no S3 ele irá usar esta chave como parâmetro para a busca. Por se tratar de um valor dinâmico você pode usar placeholders que serão substituidos pelos valores de outras chaves da configuração. Ex.: `--aws-prefix backup/ieducar_{date}`. Neste exemplo, na data 12/12/2018 o prefixo será `backup/ieducar_2018-12-12`. |
| newbase | Por padrão o sistema irá baixar a base sendo buscada e irá restaurar com o mesmo nome. Isso quer dizer que se o banco já existir ele será sobrescrevido. Para evitar isto você pode informar um nome alternativo para a nova base. Ex.: `--newbase nova_restauracao`. A exemplo da chave `aws-prefix` você também pode usar placeholders no nome da nova base. Ex.: `--newbase ieducar_{year}_{month}_{day}`. |
| jobs | Número de jobs usados pelo postgre ao restaurar a base. Isso pode melhorar consideravelmente o tempo usado para isso. Está disponível a partir da versão 8.4 do postgres |

Além das chaves de configuração informadas acima, internamente você também tem
acesso às seguintes chaves para serem usadas como placeholders:

- `year`
- `month`
- `day`

### Configuração global

Além de poder informar todos os parâmetros pela linha de comando,  através de um
arquivo de configuração na pasta onde o script está rodando ou em outro local
indicado pela opção `--config-file` você pode criar uma configuração global em:

```
~/.config/i-educar-restore/config.ini
```

Assim o script irá funcionar com estas configurações não importando a pasta de
onde for chamado.

## Gerando o phar

Para gerar um arquivo *.phar a partir do repositório e recomendado usar o
[Box](https://box-project.github.io/box2/).

Após instalar o Box (via phar ou como um pacote composer global), basta rodar o
seguinte comando na raíz do projeto:

```
$ box build
```
