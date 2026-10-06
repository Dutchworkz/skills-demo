---
name: unit-tests
version: 1.2.0
description: Gebruik bij het schrijven, aanvullen of reviewen van .NET-unit tests volgens de Dutchworkz-conventies.
---

# Unit tests volgens Dutchworkz-conventies

Pas deze conventies toe wanneer je unit tests schrijft of reviewt voor C#-code.

## Stack

- **Framework:** xUnit
- **Assertions:** FluentAssertions (`resultaat.Should().Be(...)`) — nooit `Assert.Equal`
- **Mocking:** NSubstitute — nooit Moq
- **Testdata:** Bogus voor realistische data waar dat de test leesbaarder maakt

## Structuur

1. Eén testklasse per geteste klasse: `ProductServiceTests` voor `ProductService`.
2. Elke test volgt het AAA-patroon met witregels tussen de blokken en zonder `// Arrange`-commentaar; de structuur moet zichtbaar zijn zonder commentaar.
3. Naamgeving: `MethodeNaam_Should_VerwachtGedrag_When_Conditie`, bijvoorbeeld `ApplyDiscount_Should_ThrowValidationException_When_PercentageIsNegative`.
4. Gebruik een private factory-methode `CreateSut()` voor het opbouwen van het te testen object met gemockte dependencies.

## Regels

- Gebruik vóór het schrijven van tests de Superpowers-skill `test-driven-development` en volg de RED-GREEN-REFACTOR-cyclus; deze skill bepaalt de Dutchworkz-conventies voor de testopmaak.
- Eén logische assert per test; meerdere `Should()`-aanroepen op hetzelfde resultaatobject zijn toegestaan.
- Geen magic strings of numbers: gebruik betekenisvolle constanten in de test.
- Test gedrag, geen implementatie: verifieer alleen interacties met mocks als de interactie zélf het contract is.
- Edge cases verplicht: null-input, lege collecties en grenswaarden krijgen eigen tests.
- Geen `[Fact]` met logica in een loop; gebruik `[Theory]` met `[InlineData]` of `[MemberData]`.

## Voorbeeld

```csharp
public class ProductServiceTests
{
    private readonly IProductRepository _repository = Substitute.For<IProductRepository>();

    private ProductService CreateSut() => new(_repository);

    [Fact]
    public void ApplyDiscount_Should_ThrowValidationException_When_PercentageIsNegative()
    {
        var sut = CreateSut();
        var product = new Product(Id: 1, Name: "Demo", Price: 100m);
        _repository.GetById(product.Id).Returns(product);

        var act = () => sut.ApplyDiscount(product.Id, percentage: -10m);

        act.Should().Throw<ValidationException>()
           .WithMessage("*percentage*");
    }
}
```
