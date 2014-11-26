# act_as_cnpj_cpf

[![Build Status](https://travis-ci.org/josuelima/act_as_cnpj_cpf.svg?branch=master)](https://travis-ci.org/josuelima/act_as_cnpj_cpf)
[![Code Climate](https://codeclimate.com/github/josuelima/act_as_cnpj_cpf/badges/gpa.svg)](https://codeclimate.com/github/josuelima/act_as_cnpj_cpf)
[![Test Coverage](https://codeclimate.com/github/josuelima/act_as_cnpj_cpf/badges/coverage.svg)](https://codeclimate.com/github/josuelima/act_as_cnpj_cpf)

** Rubygem to format and validate CNPJ (Brazilian company registration number) and CPF (Brazilian citizens registration number). You can use it solo or as an ActiveRecord plugin. **

act_as_cnpj_cpf é uma Rubygem para validar e formatar CNPJ e CPF.

## Funcionalidades

* Valida CPF e CNPJ com ou sem formatação
* Integração com ActiveRecord
* Formata número para o padrão do CNPJ ou CPF (xx.xxx.xxx/xxxx-xx e xxx.xxx.xxx-xx)
* Possibilidade de utilizar um atributo como CNPJ ou CPF ao mesmo tempo
* Permite persistir cpf ou cnpj inválido (caso seja especificado com permite_invalido: true)

Este projeto foi inicialmente desenvolvido como uma extensão da gem [brcpfcnpj](https://github.com/tapajos/brazilian-rails/tree/master/brcpfcnpj) e em seguida totalmente re-escrito por se tratar de uma proposta diferente. Além das novas funcionalidades, diferentemente do brcpfcnpj, este projeto utiliza o número sem formatação como padrão para salvar no banco de dados e exibir (podendo ser exibido formatado com .formatado)

## Instalação

```bash
$ gem install act_as_cnpj_cpf
```

ou adicione act_as_cnpj_cpf ao seu Gemfile e `bundle install`:

```ruby
gem 'act_as_cnpj_cpf'
```

## Uso

### ActiveRecord

CPF Válido

```ruby
class Pessoa < ActiveRecord::Base
  # :cpf é um atributo no seu model Pessoa
  usar_como_cpf :cpf
end

fulano = Pessoa.new(cpf: '67392957864')
# ou
fulano = Pessoa.new(cpf: '673.929.578-64')

fulano.cpf # retorna numero sem formatação 67392957864
fulano.cpf.formatado # retorna 673.929.578-64
fulano.cpf.valido? # retorna true
fulano.valid? # retorna true
fulano.save # retorna true e salva sem formatação no cpf
```

CPF Inválido

```ruby
fulano = Pessoa.new(cpf: '111111')
fulano.cpf # retorna 111111
fulano.cpf.valido? # retorna false
fulano.cpf.formatado # retorna 111111
fulano.valid? # retorna false
fulano.save # retorna false e adiciona mensagem de erro ao model (fulano.errors)
```

CNPJ Válido

```ruby
class Empresa < ActiveRecord::Base
  # :cnpj é um atributo no seu model Empresa
  usar_como_cnpj :cnpj
end

company = Empresa.new(cnpj: '54609346436407')
# ou
company = Empresa.new(cnpj: '54.609.346/4364-07')

company.cnpj # retorna numero sem formatação 54609346436407
company.cnpj.formatado # retorna 54.609.346/4364-07
company.cnpj.valido? # retorna true
company.valid? # retorna true
company.save # retorna true e salva sem formatação no cnpj
```

CPF Inválido

```ruby
company = Empresa.new(cnpj: '1111111111111')
company.cnpj # retorna 1111111111111
company.cnpj.valido? # retorna false
company.cnpj.formatado # retorna 1111111111111
company.valid? # retorna false
company.save # retorna false e adiciona mensagem de erro ao model (company.errors)
```

### CNPJ e CPF no mesmo atributo

Podem existir situações onde você precise utilizar o mesmo campo ora como CNPJ ora como CPF, para isto basta utilizar `usar_como_cnpj_ou_cpf`:

```ruby
class Cliente < ActiveRecord::Base
  # :codigo é um atributo no seu model Cliente
  usar_como_cnpj_ou_cpf :codigo
end

# para cnpj
cliente = Cliente.new(codigo: '54609346436407')
# ou para cpf
company = Cliente.new(codigo: '67392957864')

company.codigo # retorna o cpf ou cnpj informado sem formatação
company.codigo.formatado # retorna o cpf ou cnpj informado devidamente formatado
company.codigo.valido? # retorna true caso seja um cnpj ou cpf válido
company.valid? # retorna true caso seja um cnpj ou cpf válido
company.save # retorna true e salva sem formatação caso seja um cnpj ou cpf válido
```