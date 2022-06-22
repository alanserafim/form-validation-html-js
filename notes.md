# Anotações do Curso: Validação de Formulário e HTML5


## Atributos de validação
    
    Propriedades da tag input

        *required
        *type="text"/ "number" / "email" / "password"
        *minlength="6"
        *title="something"
        *pattern="regex"

## Mensagems de erro
    No html: atributo title=""
    No JS: input.setCustomValidity('Mensagem customizada de erro')



## Validação do campo email

    type="email"
    
## Validaçaõ do campo senha
     
     type="password"
     pattern="^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,12}$" 
     title="A senha deve conter entre 6 e 12 caracteres, deve conter pelo menos uma letra maiúscula, uma letra minúscula e um número."

### Regex

    https://regexlib.com/?AspxAutoDetectCookieSupport=1
    https://regexr.com/


## Validação de data de nascimento

    type="date"
    data-tipo="dataNascimento"
    JS: if(erro) input.setCustomValidity(mensagem);

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

    function validaDataNascimento(input) {
        const dataRecebida = new Date(input.value)
        let mensagem = ''
        if(!maiorQue18(dataRecebida)) {
            mensagem = 'Você deve ser maior que 18 anos para se cadastrar.'
        }
        input.setCustomValidity(mensagem)
    }

    function maiorQue18(data) {
        const dataAtual = new Date()  // data atual
        const dataMais18 = new Date(data.getUTCFullYear() + 18, data.getUTCMonth(), data.getUTCDate())  // somando 18 ano a data atual
        return dataMais18 <= dataAtual
    }

    ==============================================> app.js
    import { valida } from "./validacao.js";

    const inputs = document.querySelectorAll('input');

    inputs.forEach(input => {
        input.addEventListener('blur', (evento) => {
            valida(evento.target)
        })
    })



    
    
