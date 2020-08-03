# Enum

O Enum é um tipo de classe estática, ou seja, é um objeto a ser reutilizado mas que porém não passará por modificações. Esse tipo de objeto não precisa
ser mantido em uma tabela no banco de dados, uma vez que não passará por modificações. Esse é o cenário para a utilização do tipo Enum.

## Formas de utilizar o Enum

##### Modelo:

A baixo temos uma clase 'Serie', e uma de suas propriedades é o Turno:

```
public class Serie
{
      public int Id { get; set; }
      public string Nome { get; set; }
      public int Turno { get; set; }
}
```

Uma vez que os turnos são fixos (não passam por mudanças) podemos aplicar o tipo Enum para reutilizar as informações do tipo Turno, conforme a seguir. 

##### Enum
``` 
public enum ETurno
{
      [Display(Name = "Turno 1 - Manhã")]
      Manha = 1,
 
      [Display(Name = "Turno 2 - Tarde")]
      Tarde = 2,
 
      [Display(Name = "Turno 3 - Noite")]
      Noite = 3,      
}
```

Percebam que o nosso Enum possui três informações em cada propriedade, um inteiro e duas strings. O 'Display' pode ser utilizado quando queremos mostrar na View uma sequência de palavras / frase. 

Percebam também que na Classe 'Serie', declarada anteriormente, o Turno é armazenado como um inteiro. Isso significa que iremos armazenar o valor inteiro da propriedade do Enum, em nosso exemplo temos os valores 1, 2 e 3, representando Manhã, Tarde e Noite. 

Poderíamos porém, armazenar qualquer uma das propriedades no banco de dados, bastando mudar a forma de implementação, e modificar o tipo do Turno declarado na Classe 'Serie'.

A seguir, veremos como recuperar os valores para apresentar na View.


##### Recuperar os valores diretamente na View:

```
@model Serie

<select class="form-control" asp-for="Turno" asp-items="Html.GetEnumSelectList<ETurno()"> 
</select>
```

O resultado desse código irá produzir a seguinte saída HTML:

```
<select class="form-control" id"Turno" name="Turno"> 
      <option  value="1">Manha</option>
      <option  value="2">Tarde</option>
      <option  value="3">Noite</option>
</select>    
```


###### EnumHelper

Imaginemos agora que queiramos mostrar a propriedade 'Display' do Enum. Nesse caso teremos que fazer uma manipulação, adicionando uma classe extra (EnumHelper), que você pode baixar neste diretório. 

Com a classe 'EnumHelper' acima implementada, vamos chamar a rotina diretamente na View da seguinte forma:

```
@model Serie

<select asp-for="Turno" class="form-control">       
      @foreach (var value in @EnumHelper<ETurno>.GetValues(ETurno.Manha))
      {                
            var description = EnumHelper<ETurno>.GetDisplayValue(value);
            <option value="@value">@description</option>                                
      }
</select>  
```

O resultado desse código irá produzir a seguinte saída HTML:

```
<select class="form-control" id"Turno" name="Turno"> 
      <option  value="1">Turno 1 - Manhã</option>
      <option  value="2">Turno 2 - Tarde</option>
      <option  value="3">Turno 3 - Noite</option>
</select>    
```


##### Manipular o Enum no Controller:

Imaginemos que você queira manipular o Enum no Controlador antes de passar para a View, segue o exemplo:

###### ViewModel

```
public class EnumViewModel
{
      public int Id { get; set; }
      public string Name { get; set; }
}
``` 

###### Controller

```
public ActionResult MostrarTurnos()
{
      IList<TurnoViewModel> listaTurno = new List<TurnoViewModel>();
      foreach (int item in Enum.GetValues(typeof(ETurno)))
      {
            TurnoViewModel turno = new TurnoViewModel();
            turno.TurnoName = Enum.GetName(typeof(ETurno), item);
            turno.TurnoId = item;
            listaTurno.Add(turno);
      }
      return View(listaTurno);
}
```

Perceba que utilizamos uma classe adicional, na verdade uma ViewModel, com objetivo de manter a tipagem da View. No Controller pegamos os valores do Enum e adicionamos a uma 'listaTurno', que é do tipo da ViewModel que criamos (TurnoViewModel). 

Por fim, retornamos a view passando a lista de turnos.

Abaixo, iremos recuperar os valores passados pelo Controlador.

###### View:

```
@model IEnumerable<EnumViewModel>

<select class="form-control" asp-for="Id"> 
      @foreache (var item in Model) 
      {
            <option value="@item.Id">@item.Name</option>   
      }
</select>
```

O resultado produzido no HTML:

```
<select class="form-control" id"Turno" name="Turno"> 
      <option  value="1">Manha</option>
      <option  value="2">Tarde</option>
      <option  value="3">Noite</option>
</select>    
```
