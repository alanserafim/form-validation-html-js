# Anotações do Curso: Validação de Formulário e HTML5


## Atributos de validação
    
    Propriedades da tag input

        *required => obrigatório
        *type= "text"/ "number" / "email" / "password" / "date" / ... => tipo do input
        *minlength="6"  => tamanho minimo do campo em caracteres
        *title="mensagem de erro"
        *pattern="regex"
        *Consultar referência.

    Modelo:
         <div class="input-container">
            <input name="" id="" class="input" type="text" placeholder="" data-tipo="" required>
            <label class="input-label" for="cpf">CPF</label>
            <span class="input-mensagem-erro">Este campo não está válido</span>
        </div>


    Referências:
        https://developer.mozilla.org/pt-BR/docs/Web/HTML/Element/input
        https://www.w3schools.com/tags/tag_input.asp



## Mensagens de erro

    No html: atributo title=""
    No JS: input.setCustomValidity('Mensagem customizada de erro')


## Validação do campo email

    type="email"
    

## Validaçaõ do campo senha
     
     type="password"
     pattern="^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,12}$" 
     title="A senha deve conter entre 6 e 12 caracteres, deve conter pelo menos uma letra maiúscula, uma letra minúscula e um número."

### Regex

    Referências:
        https://regexlib.com/?AspxAutoDetectCookieSupport=1
        https://regexr.com/


## Validação de data de nascimento

    type="date"
   

### Verificando idade maior que 18 anos com JS:
        
        const dataNascimento = document.querySelector('#nascimento');

        dataNascimento.addEventListener('blur', (evento) => {
            validaDataNascimento(evento.target);
        })

        function validaDataNascimento(input) {
            const dataRecebida = new Date(input.value);
            let mensagem = "";
            if(!maiorQue18(dataRecebida)){
                mensagem = "Você deve ser maior que 18 anos para se cadastrar.";
            }
            input.setCustomValidity(mensagem);
        }

        function maiorQue18(data) {
            const dataAtual = new Date() // data atual
            const dataMais18 = new Date(data.getUTCFullYear() + 18, data.getUTCMonth(), data.getUTCDate()); // somando 18 ano a data atual
            return dataMais18 <= dataAtual;
        }

## Adicionando mensagem de erros personalizadas com o javascript

    ==============================================> validacao.js
    export function valida(input) {
    const tipoDeInput = input.dataset.tipo
    if(validadores[tipoDeInput]) {
        validadores[tipoDeInput](input)
    }

    if(input.validity.valid){
        input.parentElement.classList.remove('input-container--invalido')
        input.parentElement.querySelector('.input-mensagem-erro').innerHTML = ''
    } else {
        input.parentElement.classList.add('input-container--invalido')
        input.parentElement.querySelector('.input-mensagem-erro').innerHTML = mostraMensagemDeErro(tipoDeInput, input)
    }
    }

    //atributos da propriedade/objeto validity do input
    const mensagensDeErro = {
        nome: {
            valueMissing: 'O campo nome não pode estar vazio.'
        },
        email:{
            valueMissing: 'O campo de email não pode estar vazio.',
            typeMismatch: 'O email digitado não é válido'
        },
        senha: {
            valueMissing: 'O campo senha não pode estar vazio.',
            patternMismatch: 'A senha deve conter entre 6 e 12 caracteres, deve conter pelo menos uma letra maiúscula, uma letra minúscula e um número.'
        },
        dataNascimento: {
            valueMissing: 'O campo de data de nascimento não pode estar vazio.',
            customError: 'Você deve ser maior que 18 anos para se cadastrar.'
        }
    }


    const validadores = {
        dataNascimento:input => validaDataNascimento(input)
    }

    const tiposDeErro = [
        'valueMissing', 
        'typeMismatch',
        'patternMismatch',
        'customError'
    ]

    function mostraMensagemDeErro(tipoDeInput, input) {
        let mensagem = ''
        tiposDeErro.forEach(erro => {
            if(input.validity[erro]){
                mensagem = mensagensDeErro[tipoDeInput][erro]
            }
        })
        return mensagem
    }

    ==============================================> app.js
    import { valida } from "./validacao.js";

    const inputs = document.querySelectorAll('input');

    inputs.forEach(input => {
        input.addEventListener('blur', (evento) => {
            valida(evento.target)
        })
    })



## Validação de CPF
    
    / ================> Validação CPF

    function validaCPF(input) {
        const cpfFormatado = input.value.replace(/\D/g, '')
        let mensagem = ''

        if(!checaCPFRepetido(cpfFormatado) || !checaEstruturaCPF(cpfFormatado)) {
            mensagem = 'O CPF digitado não é válido.'
        }

        input.setCustomValidity(mensagem)
    }

    function checaCPFRepetido(cpf) {
        const valoresRepetidos = [
            '00000000000',
            '11111111111',
            '22222222222',
            '33333333333',
            '44444444444',
            '55555555555',
            '66666666666',
            '77777777777',
            '88888888888',
            '99999999999'
        ]
        let cpfValido = true

        valoresRepetidos.forEach(valor => {
            if(valor == cpf) {
                cpfValido = false
            }
        })

        return cpfValido
    }

    function checaEstruturaCPF(cpf) {
        const multiplicador = 10

        return checaDigitoVerificador(cpf, multiplicador)
    }

    function checaDigitoVerificador(cpf, multiplicador) {
        if(multiplicador >= 12) {
            return true
        }

        let multiplicadorInicial = multiplicador
        let soma = 0
        const cpfSemDigitos = cpf.substr(0, multiplicador - 1).split('')
        const digitoVerificador = cpf.charAt(multiplicador - 1)
        for(let contador = 0; multiplicadorInicial > 1 ; multiplicadorInicial--) {
            soma = soma + cpfSemDigitos[contador] * multiplicadorInicial
            contador++
        }

        if(digitoVerificador == confirmaDigito(soma)) {
            return checaDigitoVerificador(cpf, multiplicador + 1)
        }

        return false
    }

    function confirmaDigito(soma) {
        return 11 - (soma % 11)
    }


## Validando CEP e integrando a API via CEP

    /============================> recebendo dados de endereco

    function recuperarCEP(input) {
        const cep = input.value.replace(/\D/g, "")
        const url = `https://viacep.com.br/ws/${cep}/json/`
        const options = {
            method: 'GET',
            mode: 'cors',
            headers: {
                'content-type': 'application/json;charset=utf-8',
            }
        }

        if(!input.validity.patternMismatch && !input.validity.valueMissing){
            fetch(url, options).then(
                response => response.json()
            ).then(
                data => {
                    if(data.erro) {
                        input.setCustomValidity('Não foi possível buscar o CEP')
                        return
                    }
                    input.setCustomValidity('')
                    preencheCamposComCEP(data)
                    return
                }
                
            )
        }
    }

    function preencheCamposComCEP(data) {
        const logradouro = document.querySelector('[data-tipo="logradouro"]')
        const cidade = document.querySelector('[data-tipo="cidade"]')
        const estado = document.querySelector('[data-tipo="estado"]')

        logradouro.value = data.logradouro
        cidade.value = data.localidade
        estado.value = data.uf
}


## Validando preço e aplicando máscara monetária

    https://github.com/codermarcos/simple-mask-money

    import { valida } from "./validacao.js";

    const inputs = document.querySelectorAll('input');

    inputs.forEach(input => {
        if(input.dataset.tipo === 'preco') {
            SimpleMaskMoney.setMask(input, {
                prefix: 'R$ ',
                fixed: true,
                fractionDigits: 2,
                decimalSeparator: ',',
                thousandsSeparator: '.',
                cursor: 'end'
            })
        }
        input.addEventListener('blur', (evento) => {
            valida(evento.target)
        })
    })