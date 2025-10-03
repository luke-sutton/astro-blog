---
title: 'My First Blog Post'
pubDate: 2022-07-01
description: 'This is the first post of my new Astro blog.'
author: 'Astro Learner'
image:
url: 'https://docs.astro.build/assets/rose.webp'
alt: 'The Astro logo on a dark background with a pink glow.'
tags: ["astro", "blogging", "learning in public"]
---

# Cookbook - OOP Console App

Tutorial on creating a simple console application to demonstrate the main pillars of object orientated programing,
Polymorphism, Inheritance, and Interfaces.

App description:
: If some recipes have been saved, when the application starts it should print all existing recipes.   
The app should then print 'Create a new cookie recipe! Available ingredients are:' then print the available
ingredients.   
The user should then be able to select a choice of ingredients and then save a new recipe.

### Creating the basic app structure

Create a new C# console application called Cookie Cookbook in your IDE.

Delete the contents of the program.cs file and replace with:

```c#
var cookiesRecipesApp = new CookiesRecipesApp();
cookiesRecipesApp.Run();
```

Wrapping the application in a class like we have done here allows the program.cs file to be kept clean, it encapsulates
the main execution logic for our application, so that the main entry point, Program.cs can remain simple and focused.

Writing the names of classes before implementing them, as shown above (CookiesRecipesApp), is a common practice. It
allows you to design your code as intended and focus on the overall structure. Once the class is named, you can use the
IDE to highlight it and automatically generate its structure, saving time and effort.

Add the CookiesRecipesApp class (or have the IDE generate it for you):

```C#
var cookiesRecipesApp = new CookiesRecipesApp();
cookiesRecipesApp.Run();

public class CookiesRecipesApp
{ 
    public void Run()
    {
        
    }
}
```

Look through the steps in the app description. There are two main parts to the application, saving and retrieving
recipes and interacting with the user. The CookiesRecipesApp class should not implement these steps.
To stick to the single responsibility principle, a class should have one job. The only reason this class should change
is if the main workflow described in the app description was to change.

For handling saving and retrieving recipes are type called RecipesRepository sounds sensible, and for handling
user interaction through the console a type called RecipesConsoleUserInteraction.
Since these types will be dependencies of the CookiesRecipesApp class, they should be declared as private read-only
fields, and initialized in the constructor.

First add the fields:

```C#
public class CookiesRecipesApp
{ 
    private readonly RecipesRepository _recipesRepository;
    private readonly RecipesConsoleUserInteraction _recipesConsoleUserInteraction;
    
    public void Run()
    {
        
    }
}
```

Then add the constructor:

```C#
public class CookiesRecipesApp
{
    private readonly RecipesRepository _recipesRepository;
    private readonly RecipesConsoleUserInteraction _recipesConsoleUserInteraction;

    public CookiesRecipesApp(RecipesRepository recipesRepository,
     RecipesConsoleUserInteraction recipesConsoleUserInteraction)
    {
        _recipesRepository = recipesRepository;
        _recipesConsoleUserInteraction = recipesConsoleUserInteraction;
    }

    public void Run()
    {
    }
}
```

We are again here using non-existing types to allow us to focus on writing the application structure.   
Next, let's design the main workflow of the application using these currently non-existing types.

Step 1 - See if there are any existing recipes. So this will involve calling a read
method (which we are yet to create) and I imagine passing in a file path as a parameter:

```C#
public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
    }
```

Step 2 - Printing the existing recipes. Displaying information on screen is a way of interacting with the user,
so we will use the recipesUserInteraction class to do it.

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);
    }
```

Step 3 - Prompting the user to create a recipe.

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesConsoleUserInteraction.PromptToCreateRecipe();
    }
```

I am not sure at this stage, but I think that displaying the ingredients will be a part of this step, so I won't make
it a separate action.

Step 4 - Reading the ingredients from the user.

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesConsoleUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesCOnsoleUserInteraction.ReadIngredientsFromUser();
    }
```

Next we want to check if the user has selected any ingredients:

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesConsoleUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesConsoleUserInteraction.ReadIngredientsFromUser();

        if (ingredients.Count > 0)
        {
            
        }
        else
        {
            
        }
    }
```

Lets start with if none have been selected as that is the easier path:

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesConsoleUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesConsoleUserInteraction.ReadIngredientsFromUser();

        if (ingredients.Count > 0)
        {
            
        }
        else
        {
            _recipesConsoleUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                "Recipe Will not be saved");
        }
    }
```

If the user has chosen some ingredients we want to save the new recipe:

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesConsoleUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesConsoleUserInteraction.ReadIngredientsFromUser();

        if (ingredients.Count > 0)
        {
            var recipe = new Recipe(ingredients);
        }
        else
        {
            _recipesConsoleUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                "Recipe Will not be saved");
        }
    }
```

Then we want to save the new recipe along with the existing ones to a file.

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesConsoleUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesConsoleUserInteraction.ReadIngredientsFromUser();

        if (ingredients.Count > 0)
        {
            var recipe = new Recipe(ingredients);
            allRecipes.Add(recipe);
            _recipesRepository.Write(filePath, allRecipes);
        }
        else
        {
            _recipesConsoleUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                "Recipe Will not be saved");
        }
    }
```

Next, we want to display a message that the recipe has been added and print it to the console.

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesConsoleUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesConsoleUserInteraction.ReadIngredientsFromUser();

        if (ingredients.Count > 0)
        {
            var recipe = new Recipe(ingredients);
            allRecipes.Add(recipe);
            _recipesRepository.Write(filePath, allRecipes);

            _recipesConsoleUserInteraction.ShowMessage("Recipe added:");
            _recipesConsoleUserInteraction.ShowMessage(recipe.ToString());
        }
        else
        {
            _recipesConsoleUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                "Recipe Will not be saved");
        }
    }
```

The last step is to exit the application

```C#
    public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesConsoleUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesConsoleUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();

        if (ingredients.Count > 0)
        {
            var recipe = new Recipe(ingredients);
            allRecipes.Add(recipe);
            _recipesRepository.Write(filePath, allRecipes);

            _recipesConsoleUserInteraction.ShowMessage("Recipe added:");
            _recipesConsoleUserInteraction.ShowMessage(recipe.ToString());
        }
        else
        {
            _recipesConsoleUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                "Recipe Will not be saved");
        }

        _recipesUserInteraction.Exit();
    }
```

That is the high level design finished. Even though the code for the classes is not yet written, someone would be
able to read through this and get a basic understanding of what the app does.

Now generate the 2 classes, this can be done automatically using the IDE by selecting them and choosing the appropriate
option. They will be added to the bottom of the file, change the access level from internal to public:

```C#
public class RecipesConsoleUserInteraction
{
}

public abstract class RecipesRepository
{
}
```

### Dependency Inversion and Dependency Injection

First, lets implement some of the simple functions required in our user interaction class, lets start with
ShowMessage and Exit.

These can again be auto generated by the IDE by highlighting them and selecting the appropriate option:

```C#
public class RecipesConsoleUserInteraction
{
    public void ShowMessage(string noIngredientsHaveBeenSelectedRecipeWillNotBeSaved)
    {
        throw new NotImplementedException();
    }

    public void Exit()
    {
        throw new NotImplementedException();
    }
}
```

Then update the functions:

```C#
public class RecipesConsoleUserInteraction
{
    public void ShowMessage(string message)
    {
        Console.WriteLine(message);
    }

    public void Exit()
    {
        Console.WriteLine("Press any key to close.");
        Console.ReadKey();
    }
}
```

Lets take a closer look at the constructor:

```C#
private readonly RecipesRepository _recipesRepository;
private readonly RecipesConsoleUserInteraction _recipesConsoleUserInteraction;

public CookiesRecipesApp(RecipesRepository recipesRepository,
        RecipesConsoleUserInteraction recipesConsoleUserInteraction)
    {
        _recipesRepository = recipesRepository;
        _recipesConsoleUserInteraction = recipesConsoleUserInteraction;
    }
```

Why do we pass the dependencies to the constructor instead of creating them like this and removing the constructor:

```C#
private readonly RecipesRepository _recipesRepository = new RecipesRepository();
private readonly RecipesConsoleUserInteraction _recipesConsoleUserInteraction = new RecipesConsoleUserInteraction();
```

Note:
: you can shorten this to -
```c#
private readonly RecipesRepository _recipesRepository = new ();
private readonly RecipesConsoleUserInteraction _recipesConsoleUserInteraction = new ();
```
This is possible as the type is already declared at the beginning. This is called target-typed expression, and
has been available since c#9

A class should have a single responsibility, so our CookiesRecipesApp purpose is the main workflow of the
application, and should only need to be changed if the workflow changes.

But what if it is decided that instead of interacting with the user via the console, we should do it by a graphical
interface? We would have to change the RecipesConsoleUserInteraction class being instantiated within our class to
a more appropriate name, and we would possibly change the methods being called on it. This means that our class now
has more than one reason to change and breaks the single responsibility principle.

This also means that our classes are tightly coupled, this makes it harder to reuse a class without involving the
other, and changing one class could break the other.

This means that our application breaks the D in the SOLID principles, the Dependency Inversion Principle.   
This principle states that high-level modules should not depend on low-level modules; both should depend on
abstractions.   
This means that dependencies of a type should not be concrete; they should be abstractions.   
Abstractions in c# usually mean interfaces.

To implement this, start by changing the type in our field from RecipesConsoleUserInteraction to an interface
called IRecipesUserInteraction. Then change the name of the field to _recipesUserInteraction, you can have the IDE
change all instances in the app for you:

```C#
    private readonly RecipesRepository _recipesRepository;
    private readonly IRecipesUserInteraction _recipesUserInteraction;
```

Then again use the IDE to generate this interface. Make it public and add the methods from our
RecipesConsoleUserInteraction to it, then have our class implement the interface:

```C#
public interface IRecipesUserInteraction
{
    void ShowMessage(string message);
    void Exit();
}

public class RecipesConsoleUserInteraction : IRecipesUserInteraction
{
    public void ShowMessage(string message)
    {
        Console.WriteLine(message);
    }

    public void Exit()
    {
        Console.WriteLine("Press any key to close.");
        Console.ReadKey();
    }
}
```

As you can see, our interface makes no mention of the console, It only states what methods are required to interact
with the user.

Going back to our main class, if we are unable to instantiate the class or interface within it, the solution is to
inject it into the constructor. This is called Dependency Injection. Dependency Injection means the class is given
the dependencies it needs; it doesn't create them itself.

```C#
    private readonly RecipesRepository _recipesRepository;
    private readonly IRecipesUserInteraction _recipesUserInteraction;

    public CookiesRecipesApp(RecipesRepository recipesRepository,
        IRecipesUserInteraction recipesUserInteraction)
    {
        _recipesRepository = recipesRepository;
        _recipesUserInteraction = recipesUserInteraction;
    }
```

Repeat this process for the RecipesRepository class:

```C#
private readonly IRecipesRepository _recipesRepository;
    private readonly IRecipesUserInteraction _recipesUserInteraction;

    public CookiesRecipesApp(IRecipesRepository recipesRepository,
        IRecipesUserInteraction recipesUserInteraction)
    {
        _recipesRepository = recipesRepository;
        _recipesUserInteraction = recipesUserInteraction;
    }
```

```C#
public interface IRecipesRepository
{
}
```

```C#
public class RecipesRepository : IRecipesRepository
{
}
```

Now we must adjust how this class is created:

```C#
var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(),
    new RecipesConsoleUserInteraction());
cookiesRecipesApp.Run();
```

So now looking at our CookiesRecipesApp, in the scenario we mentioned earlier that a new method of interacting with the
user was requested, what would we have to change? Absolutely nothing, the only mentions are that some interaction is
required with the user not how it is done. The only thing that would need to be changed is how the class is called;
instead of passing in new RecipesConsoleUserInteraction(), the new class would be passed instead.

### Designing the types

Now we will focus on designing the types our application needs.   
At the moment I can think of two types we will need, recipes and ingredients. Lets create new namespaces for them.

Add a new folder to the project called Recipes, then inside the folder add a new class file called Recipe:

```C#
namespace Cookie_Cookbook.Recipes;

public class Recipe
{
    
}
```

Our Recipe class will simply be a wrapper for a collection of ingredients.

```C#
namespace Cookie_Cookbook.Recipes;

public class Recipe
{
    public List<Ingredient> Ingredients { get; }

    public Recipe(List<Ingredient> ingredients)
    {
        Ingredients = ingredients;
    }
}
```

Lets also add the Ingredient type, and let's make it abstract:

```C#
namespace Cookie_Cookbook.Recipes;

public class Recipe
{
    public List<Ingredient> Ingredients { get; }

    public Recipe(List<Ingredient> ingredients)
    {
        Ingredients = ingredients;
    }
}

public abstract class Ingredient
{
}
```

Abstract Types:
: A class marked as abstract means that you cant directly create an instance of the class, it is meant to provide
a common structure for other classes to inherit and implement.

As this list is public, even if it only has a getter it presents a risk. Anyone who uses our recipes object can call
any method on this list, such as recipe.Ingredients.Clear(), which would remove all ingredients from our recipe. We do
not want this. We can avoid this by changing the List type to IEnumerable:

```C#
public class Recipe
{
    public IEnumerable<Ingredient> Ingredients { get; set; }

    public Recipe(IEnumerable<Ingredient> ingredients)
    {
        Ingredients = ingredients;
    }
}
```

IEnumerable is an interface that is implemented by almost every collection in c#.   
Let's inspect the IEnumerable type to see more information on it:

```C#
namespace System.Collections.Generic
{
  public interface IEnumerable<out T> : IEnumerable
  {
    IEnumerator<T> GetEnumerator();
  }
}
```

In c# angle brackets mean that a type or method is generic, this means it is parameterized by another type.   
For example:

```C#
var numbers = new List<int>();

var person = JsonSerializer.Deserialize<Person>(text);
```

Here we are creating a list of type int, and deserializing some JSON into a Person object.

The T in the IEnumerable interface above represents a type that will be passed in, so it can work with any type.

We can see that the IEnumerable interface does not expose any methods to modify a collection, it only allows data to be
iterated over and is read-only so you cannot modify, add or remove items.

Now back to our Ingredient class, each ingredient should have an ID a name and preparation instructions. They are
all pieces of data, so let's make them properties. The ID and name should be abstract as our abstract class should
not provide any default implementation of them, but the PreparationInstruction property can be virtual as a default
value is suitable here, and can be overridden if required.

```C#
public abstract class Ingredient
{
    public abstract int Id { get; }
    public abstract string Name { get; }
    public virtual string PreparationInstructions => "Add to other ingredients";
}
```

Abstract and Virtual Properties:
: in an abstract class a property marked as abstract must be implemented by any class derived from it. Virtual
properties can provide a default implementation, but derived class can override this to provide their own
implementation.

Next lets create concrete ingredient classes.

```C#
public abstract class Ingredient
{
    public abstract int Id { get; }
    public abstract string Name { get; }
    public virtual string PreparationInstructions => "Add to other ingredients.";
}

public class WheatFlour : Ingredient
{
    public override int Id => 1;
    public override string Name => "Wheat flour";
    public override string PreparationInstructions => $"Sieve. Add to other ingredients.";
}

public class SpeltFlour : Ingredient
{
    public override int Id => 2;
    public override string Name => "Spelt flour";
    public override string PreparationInstructions => $"Sieve. Add to other ingredients.";
}

public class Butter : Ingredient
{
    public override int Id => 3;
    public override string Name => "Butter";
    public override string PreparationInstructions => $"Melt on low heat. Add to other ingredients.";
}

public class Chocolate : Ingredient
{
    public override int Id => 4;
    public override string Name => "Chocolate";
    public override string PreparationInstructions => $"Melt in a water bath. Add to other ingredients.";
}

public class Sugar : Ingredient
{
    public override int Id => 5;
    public override string Name => "Sugar";
}

public class Cardamom : Ingredient
{
    public override int Id => 6;
    public override string Name => "Cardamom";
    public override string PreparationInstructions => $"Take half a teaspoon. Add to other ingredients.";
}

public class Cinnamon : Ingredient
{
    public override int Id => 7;
    public override string Name => "Cinnamon";
    public override string PreparationInstructions => $"Take half a teaspoon. Add to other ingredients.";
}

public class CocoaPowder : Ingredient
{
    public override int Id => 8;
    public override string Name => "Cocoa powder";
}
```

We have some code repetition here, so let's address that now.

Both types of flour have the same preparation instruction, so these could both derive from an abstract flour class
that could contain the preparation instruction.

```C#
public abstract class Flour : Ingredient
{
    public override string PreparationInstructions => $"Sieve. Add to other ingredients.";
}

public class WheatFlour : Flour
{
    public override int Id => 1;
    public override string Name => "Wheat flour";
}

public class SpeltFlour : Flour
{
    public override int Id => 2;
    public override string Name => "Spelt flour";
}
```

Now we only have one location that details the preparation instructions for flour.

Another improvement we can make to preparation instructions is here:

```C#
public override string PreparationInstructions => $"Take half a teaspoon. Add to other ingredients.";
```

The second sentence in all the override preparation instructions is identical to the text in our abstract ingredient
class, so let's inherit it from there:

```C#
public override string PreparationInstructions => $"Take half a teaspoon. {base.PreparationInstructions}";
```

Update the rest of the preparation instructions to inherit in this way where the same sentence is used.

Both Cinnamon and Cardamom are spices and share the same preparation instructions, so let's create a spice class for
them to derive from, the same way we did with flour.

```C#
public abstract class Spice : Ingredient
{
    public override string PreparationInstructions => $"Take half a teaspoon. {base.PreparationInstructions}";
}

public class Cardamom : Spice
{
    public override int Id => 6;
    public override string Name => "Cardamom";
}

public class Cinnamon : Spice
{
    public override int Id => 7;
    public override string Name => "Cinnamon";
}
```

Ingredients deserve their own namespace, and each class should have its own file.

Select each class name in turn (except Recipes class) and select the option to move to its own file.   
Then create a new folder within the Recipes folder called Ingredients and drag the class files into it (except for
Recipes class). You may need to update the namespace at the top of each class if the IDE does not update them
for you.

### LINQ, Printing data objects

Let's go back to Program.cs.

```C#
public void Run()
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();

        if (ingredients.Count > 0)
        {
            var recipe = new Recipe(ingredients);
            allRecipes.Add(recipe);
            _recipesRepository.Write(filePath, allRecipes);

            _recipesUserInteraction.ShowMessage("Recipe added:");
            _recipesUserInteraction.ShowMessage(recipe.ToString());
        }
        else
        {
            _recipesUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                       "Recipe Will not be saved");
        }

        _recipesUserInteraction.Exit();
    }
```

Now we have our data types created we can finish filling out the details of the Run()
method.

First, let's add the Read() method to the IRecipesRepository interface; it will return a collection of recipes.
We need to add a Recipe so it can't be an IEnumerable, so let's make it a list. If we highlight the method name, the
IDE will give us the option to generate the method:

```C#
public interface IRecipesRepository
{
    object Read(object filePath);
}
```

Update the type to a list of Recipes, and change the parameter to a string. We will need to import the Recipe type,
but the IDE should handle this for us.

```C#
public interface IRecipesRepository
{
    List<Recipe> Read(string filePath);
}
```

Now, let's implement this interface in our class. Right clicking the interface name in the class should give an option
to implement missing members, this will add the method for us:

```C#
public class RecipesRepository : IRecipesRepository
{
    public List<Recipe> Read(string filePath)
    {
        throw new NotImplementedException();
    }
}
```

As I don't want to start working on the file path aspect yet, we will add a dummy implementation of the method
that will return one or two recipes, and worry about it actually returning recipes from a file later:

```C#
public class RecipesRepository : IRecipesRepository
{
    public List<Recipe> Read(string filePath)
    {
        return new List<Recipe>
        {
            new Recipe(new List<Ingredient>
            {
                new WheatFlour(),
                new Butter(),
                new Sugar()
            }),
            new Recipe(new List<Ingredient>
            {
                new CocoaPowder(),
                new SpeltFlour(),
                new Cinnamon(),
            })
        };
    }
}
```

The method requires a filePath value, for now, let's just add it as a parameter in the Run method and set it to a fixed
value.

```C#
var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(),
    new RecipesConsoleUserInteraction());
cookiesRecipesApp.Run("recipes.txt");

public class CookiesRecipesApp
{
    private readonly IRecipesRepository _recipesRepository;
    private readonly IRecipesUserInteraction _recipesUserInteraction;

    public CookiesRecipesApp(IRecipesRepository recipesRepository,
        IRecipesUserInteraction recipesUserInteraction)
    {
        _recipesRepository = recipesRepository;
        _recipesUserInteraction = recipesUserInteraction;
    }

    public void Run(string filePath)
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesUserInteraction.PrintExistingRecipes(allRecipes);

        _recipesUserInteraction.PromptToCreateRecipe();

        var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();

        if (ingredients.Count > 0)
        {
            var recipe = new Recipe(ingredients);
            allRecipes.Add(recipe);
            _recipesRepository.Write(filePath, allRecipes);

            _recipesUserInteraction.ShowMessage("Recipe added:");
            _recipesUserInteraction.ShowMessage(recipe.ToString());
        }
        else
        {
            _recipesUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                       "Recipe Will not be saved");
        }

        _recipesUserInteraction.Exit();
    }
}
```

Next let's generate the PrintExistingRecipes method for our interface:

```C#
public interface IRecipesUserInteraction
{
    void ShowMessage(string message);
    void Exit();
    void PrintExistingRecipes(List<Recipe> allRecipes);
}
```

We do not need to do anything with this collect but iterate it with a foreach loop, so let's make it an
IEnumerable:

```C#
public interface IRecipesUserInteraction
{
    void ShowMessage(string message);
    void Exit();
    void PrintExistingRecipes(IEnumerable<Recipe> allRecipes);
}
```

Note:
: Unless you have a reason otherwise, it is always best to use a generic type like IEnumerable. If later an array
or recipes was passed, and we had this set to be a list, it would not work; but having it set to IEnumerable would
cause the app to keep working.

now let's implement this method (again, this can be done by highlighting the interface name it implements):

```C#
public class RecipesConsoleUserInteraction : IRecipesUserInteraction
{
    public void ShowMessage(string message)
    {
        Console.WriteLine(message);
    }

    public void Exit()
    {
        Console.WriteLine("Press any key to close.");
        Console.ReadKey();
    }

    public void PrintExistingRecipes(IEnumerable<Recipe> allRecipes)
    {
        throw new NotImplementedException();
    }
}
```

First, let's check if any recipes exist:

```C#
public void PrintExistingRecipes(IEnumerable<Recipe> allRecipes)
    {
        if (allRecipes.Count() > 0)
        {
            Console.WriteLine("Existing recipes are:" + Environment.NewLine);
            
            for(int recipeIndex = 0; recipeIndex < allRecipes.Count(); recipeIndex++)
            {
                Console.WriteLine($"*****{recipeIndex + 1}*****");
                Console.WriteLine(allRecipes[recipeIndex]);
                Console.WriteLine();
            }
        }
    }
```

Here we iterate through each recipe, then on each iteration we print the recipe index plus one, next we print the
recipe, then we print an empty line to add some space between them.

Note:
: Why is the count method available on our IEnumerable property? This is because it is an extension method available
in the LINQ library. LINQ is so heavily used in c# that is it always available without the need to import a
library.

But there is one issue in our code here, Indexing it not available on IEnumerable, so this code will not work.
But this is simple to fix by using a foreach loop instead and then using a counter instead of the indexing:

```C#
var counter = 1;
foreach(var recipe in allRecipes)
{
    Console.WriteLine($"*****{counter}*****");
    Console.WriteLine(recipe);
    Console.WriteLine();
    ++counter;
}
```

Now let's text our code so far by commenting out the code that will not yet compile:

```C#
public class CookiesRecipesApp
{
    private readonly IRecipesRepository _recipesRepository;
    private readonly IRecipesUserInteraction _recipesUserInteraction;

    public CookiesRecipesApp(IRecipesRepository recipesRepository,
        IRecipesUserInteraction recipesUserInteraction)
    {
        _recipesRepository = recipesRepository;
        _recipesUserInteraction = recipesUserInteraction;
    }

    public void Run(string filePath)
    {
        var allRecipes = _recipesRepository.Read(filePath);
        _recipesUserInteraction.PrintExistingRecipes(allRecipes);

        // _recipesUserInteraction.PromptToCreateRecipe();
        //
        // var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();
        //
        // if (ingredients.Count > 0)
        // {
        //     var recipe = new Recipe(ingredients);
        //     allRecipes.Add(recipe);
        //     _recipesRepository.Write(filePath, allRecipes);
        //
        //     _recipesUserInteraction.ShowMessage("Recipe added:");
        //     _recipesUserInteraction.ShowMessage(recipe.ToString());
        // }
        // else
        // {
        //     _recipesUserInteraction.ShowMessage("No ingredients have been selected. " +
        //                                                "Recipe Will not be saved");
        // }

        _recipesUserInteraction.Exit();
    }
}
```

Running the application displays:

```console
Existing recipes are:

*****1*****
Cookie_Cookbook.Recipes.Recipe

*****1*****
Cookie_Cookbook.Recipes.Recipe

Press any key to close.

```

It looks like we need to override the ToString method in the recipes class, as the default implementation of the
ToString method (which is inherited from the object class) prints the name of the type.   
So we need to add an override method to our recipe class to structure how we would like the ToString work:

```C#
public class Recipe
{
    public IEnumerable<Ingredient> Ingredients { get; set; }

    public Recipe(IEnumerable<Ingredient> ingredients)
    {
        Ingredients = ingredients;
    }

    public override string ToString()
    {
        return string.Join(Environment.NewLine, Ingredients.Select(ingredient =>
            $"{ingredient.Name}. {ingredient.PreparationInstructions}"));
    }

}
```

ToString is commonly overridden to provide a meaningful string representation of an object.   
We are using another LINQ method here Select, this method takes each element in a collection and converts it into
a new form, which here is a string containing the ingredient name and preparation instructions.   
String.Join takes a collection of strings and concatenates them into a single string, using the specified delimiter,
in this case Environment.NewLine.

Now, re-run the application and it should work as intended:

```console
Existing recipes are:

*****1*****
Wheat flour. Sieve.Add to other ingredients.
Butter. Melt on low heat. Add to other ingredients.
Sugar. Add to other ingredients.

*****2*****
Cocoa powder. Add to other ingredients.
Spelt flour. Sieve.Add to other ingredients.
Cinnamon. Take half a teaspoon. Add to other ingredients.

Press any key to close.

```

### Printing the ingredients

Creating the workflow of the application first allows us to easily see what we need to implement next, which is
prompting the user to create a recipe.

```C#
public void Run(string filePath)
{
    var allRecipes = _recipesRepository.Read(filePath);
    _recipesUserInteraction.PrintExistingRecipes(allRecipes);

    // _recipesUserInteraction.PromptToCreateRecipe();
    //
    // var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();
    //
    // if (ingredients.Count > 0)
    // {
    //     var recipe = new Recipe(ingredients);
    //     allRecipes.Add(recipe);
    //     _recipesRepository.Write(filePath, allRecipes);
    //
    //     _recipesUserInteraction.ShowMessage("Recipe added:");
    //     _recipesUserInteraction.ShowMessage(recipe.ToString());
    // }
    // else
    // {
    //     _recipesUserInteraction.ShowMessage("No ingredients have been selected. " +
    //                                                "Recipe Will not be saved");
    // }

    _recipesUserInteraction.Exit();
}
```

Uncomment this line, then generate the interface method:

```C#
public interface IRecipesUserInteraction
{
    void ShowMessage(string message);
    void Exit();
    void PrintExistingRecipes(IEnumerable<Recipe> allRecipes);
    void PromptToCreateRecipe();
}
```

Then implement it:

```C#
public void PrintExistingRecipes(IEnumerable<Recipe> allRecipes)
{
    if (allRecipes.Count() > 0)
    {
        Console.WriteLine("Existing recipes are:" + Environment.NewLine);

        var counter = 1;
        foreach(var recipe in allRecipes)
        {
            Console.WriteLine($"*****{counter}*****");
            Console.WriteLine(recipe);
            Console.WriteLine();
            ++counter;
        }
    }
}

public void PromptToCreateRecipe()
{
    throw new NotImplementedException();
}
```

Then write the code:

```C#
public void PromptToCreateRecipe()
{
    Console.WriteLine("Create a new cookie recipe! " +
                      "Available ingredients are:");

    foreach (var ingredient in allIngredients)
    {
        
    }
}
```

We have a problem here, we need to print all available ingredients, but we do not have an allIngredients collection.
This would be helpful to have, especially later when a user will need to select an ingredient by its ID.

Let's create a class to contain all the ingredients. Let's call it IngredientsRegister:

```C#
public class IngredientsRegister
{
    
}
```

Then pass it onto our RecipesConsoleUserInteraction class using dependency injection:

```C#
public class RecipesConsoleUserInteraction : IRecipesUserInteraction
{
    private readonly IngredientsRegister _ingredientsRegister;

    public RecipesConsoleUserInteraction(IngredientsRegister ingredientsRegister)
    {
        _ingredientsRegister = ingredientsRegister;
    }
    
    public void ShowMessage(string message)
    {
        Console.WriteLine(message);
    }
```

Next update the PromptToCreateRecipe method to call our new class and access a method called All:

```C#
public void PromptToCreateRecipe()
{
    Console.WriteLine("Create a new cookie recipe! " +
                      "Available ingredients are:");

    foreach (var ingredient in _ingredientsRegister.All)
    {
        
    }
}
```

Then add the All method to our IngredientsRegister class:

```C#
public class IngredientsRegister
{
    public IEnumerable<Ingredient> All { get; } = new List<Ingredient>
    {
        new WheatFlour(),
        new Butter(),
        new Sugar(),
        new Chocolate(),
        new Cardamom(),
        new CocoaPowder(),
        new SpeltFlour(),
        new Cinnamon()
    };
}
```

Now let's go back to our loop, we want to print each ingredient:

```C#
public void PromptToCreateRecipe()
{
    Console.WriteLine("Create a new cookie recipe! " +
                      "Available ingredients are:");

    foreach (var ingredient in _ingredientsRegister.All)
    {
        Console.WriteLine(ingredient);
    }
}
```

As before, let's create a ToString overide in the ingredient class to display the information as we want it:

```C#
public abstract class Ingredient
{
    public abstract int Id { get; }
    public abstract string Name { get; }
    public virtual string PreparationInstructions => "Add to other ingredients.";

    public override string ToString() =>
        $"{Id} - {Name}";
}
```

Trying to run the app now will result in an error, as we have added a new dependency to the console interaction class,
so we need to pass a parmeter to it:

```C#
var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(),
    new RecipesConsoleUserInteraction(new IngredientsRegister()));
```

Now running the app should display:

```console
Existing recipes are:

*****1*****
Wheat flour. Sieve.Add to other ingredients.
Butter. Melt on low heat. Add to other ingredients.
Sugar. Add to other ingredients.

*****2*****
Cocoa powder. Add to other ingredients.
Spelt flour. Sieve.Add to other ingredients.
Cinnamon. Take half a teaspoon. Add to other ingredients.

Create a new cookie recipe! Available ingredients are:
1 - Wheat flour
3 - Butter
5 - Sugar
4 - Chocolate
6 - Cardamom
8 - Cocoa powder
2 - Spelt flour
7 - Cinnamon
Press any key to close.

```

### Finding an item by one of its properties

Lets implement the next part of our app, reading the list of ingredients from the user.

As we have done in the last step, uncomment the next part of the code:

```C#
public void Run(string filePath)
{
    var allRecipes = _recipesRepository.Read(filePath);
    _recipesUserInteraction.PrintExistingRecipes(allRecipes);

    _recipesUserInteraction.PromptToCreateRecipe();
    
    var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();
    
    // if (ingredients.Count > 0)
    // {
    //     var recipe = new Recipe(ingredients);
    //     allRecipes.Add(recipe);
    //     _recipesRepository.Write(filePath, allRecipes);
    //
    //     _recipesUserInteraction.ShowMessage("Recipe added:");
    //     _recipesUserInteraction.ShowMessage(recipe.ToString());
    // }
    // else
    // {
    //     _recipesUserInteraction.ShowMessage("No ingredients have been selected. " +
    //                                                "Recipe Will not be saved");
    // }

    _recipesUserInteraction.Exit();
}
```

Generate the method in the interface by selecting the function name and having the IDE generate it for us:

```C#
public interface IRecipesUserInteraction
{
    void ShowMessage(string message);
    void Exit();
    void PrintExistingRecipes(IEnumerable<Recipe> allRecipes);
    void PromptToCreateRecipe();
    object ReadIngredientsFromUser();
}
```

update the method to be an IEnumerable of type Ingredient:

```C#
public interface IRecipesUserInteraction
{
    void ShowMessage(string message);
    void Exit();
    void PrintExistingRecipes(IEnumerable<Recipe> allRecipes);
    void PromptToCreateRecipe();
    IEnumerable<Ingredient> ReadIngredientsFromUser();
}
```

Then generate the method in the class by selecting the IRecipesUserInteraction interface it implements and selecting
to implement all missing members:

```C#
public class RecipesConsoleUserInteraction : IRecipesUserInteraction
{
    private readonly IngredientsRegister _ingredientsRegister;

    public RecipesConsoleUserInteraction(IngredientsRegister ingredientsRegister)
    {
        _ingredientsRegister = ingredientsRegister;
    }
    
    public void ShowMessage(string message)
    {
        Console.WriteLine(message);
    }

    public void Exit()
    {
        Console.WriteLine("Press any key to close.");
        Console.ReadKey();
    }

    public void PrintExistingRecipes(IEnumerable<Recipe> allRecipes)
    {
        if (allRecipes.Count() > 0)
        {
            Console.WriteLine("Existing recipes are:" + Environment.NewLine);

            var counter = 1;
            foreach(var recipe in allRecipes)
            {
                Console.WriteLine($"*****{counter}*****");
                Console.WriteLine(recipe);
                Console.WriteLine();
                ++counter;
            }
        }
    }

    public void PromptToCreateRecipe()
    {
        Console.WriteLine("Create a new cookie recipe! " +
                          "Available ingredients are:");

        foreach (var ingredient in _ingredientsRegister.All)
        {
            Console.WriteLine(ingredient);
        }
    }

    public IEnumerable<Ingredient> ReadIngredientsFromUser()
    {
        throw new NotImplementedException();
    }
}
```

How the function should work: the user should select each ingredient by its ID and if anything else is entered,
the recipe is thought to be complete.

Now implement the logic for the method :

```C#
public IEnumerable<Ingredient> ReadIngredientsFromUser()
{
    bool shallStop = false;
    var ingredients = new List<Ingredient>();

    while (!shallStop)
    {
        Console.WriteLine("Add an ingredient by its ID, " +
                          "or type anything else if finished.");
        
        var userInput = Console.ReadLine();

        if (int.TryParse(userInput, out int id))
        {
            var selectedIngredient = _ingredientsRegister.GetById(id);
            if (selectedIngredient is not null)
            {
                ingredients.Add(selectedIngredient);
            }
        }
        else
        {
            shallStop = true;
        }
    }
    
    return ingredients;
}
```

We need a way of finding the ingredient by the ID, so it makes sense to add a new method to the ingredients register
class that accepts an ID as a parameter to action this.   
The method has already been called in our logic here:

```C#
var selectedIngredient = _ingredientsRegister.GetById(id)
```

So, again, highlight the method name and select to generate the method:

```C#
public class IngredientsRegister
{
    public IEnumerable<Ingredient> All { get; } = new List<Ingredient>
    {
        new WheatFlour(),
        new Butter(),
        new Sugar(),
        new Chocolate(),
        new Cardamom(),
        new CocoaPowder(),
        new SpeltFlour(),
        new Cinnamon()
    };

    public Ingredient GetById(int result)
    {
        throw new NotImplementedException();
    }
}
```

The simplest way for us to implement this method is to iterate through each ingredient using a foreach loop, and when
a matching ID is found, return the ingredient. If no matching ID is found, return null:

```C#
public Ingredient GetById(int id)
{
    foreach (var ingredient in All)
    {
        if (ingredient.Id == id)
        {
            return ingredient;
        }
    }
    
    return null;
}
```

Now let's uncomment the rest of the code in our applications' workflow:

```C#
public void Run(string filePath)
{
    var allRecipes = _recipesRepository.Read(filePath);
    _recipesUserInteraction.PrintExistingRecipes(allRecipes);

    _recipesUserInteraction.PromptToCreateRecipe();
    
    var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();
    
    if (ingredients.Count > 0)
    {
        var recipe = new Recipe(ingredients);
        allRecipes.Add(recipe);
        _recipesRepository.Write(filePath, allRecipes);
    
        _recipesUserInteraction.ShowMessage("Recipe added:");
        _recipesUserInteraction.ShowMessage(recipe.ToString());
    }
    else
    {
        _recipesUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                   "Recipe Will not be saved");
    }

    _recipesUserInteraction.Exit();
}
```

The condition in our if statement is using the count property, but as we are now using an IEnumerable we will need
to call the count method.   
Let's comment out the line of code that writes to a filepath:

```C#
public void Run(string filePath)
{
    var allRecipes = _recipesRepository.Read(filePath);
    _recipesUserInteraction.PrintExistingRecipes(allRecipes);

    _recipesUserInteraction.PromptToCreateRecipe();
    
    var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();
    
    if (ingredients.Count() > 0)
    {
        var recipe = new Recipe(ingredients);
        allRecipes.Add(recipe);
        // _recipesRepository.Write(filePath, allRecipes);
    
        _recipesUserInteraction.ShowMessage("Recipe added:");
        _recipesUserInteraction.ShowMessage(recipe.ToString());
    }
    else
    {
        _recipesUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                   "Recipe Will not be saved");
    }

    _recipesUserInteraction.Exit();
}
```

Now run the application and check it works.

```Console
Existing recipes are:

*****1*****
Wheat flour. Sieve.Add to other ingredients.
Butter. Melt on low heat. Add to other ingredients.
Sugar. Add to other ingredients.

*****2*****
Cocoa powder. Add to other ingredients.
Spelt flour. Sieve.Add to other ingredients.
Cinnamon. Take half a teaspoon. Add to other ingredients.

Create a new cookie recipe! Available ingredients are:
1 - Wheat flour
3 - Butter
5 - Sugar
4 - Chocolate
6 - Cardamom
8 - Cocoa powder
2 - Spelt flour
7 - Cinnamon
Add an ingredient by its ID, or type anything else if finished.
1
Add an ingredient by its ID, or type anything else if finished.
3
Add an ingredient by its ID, or type anything else if finished.
r
Recipe added:
Wheat flour. Sieve.Add to other ingredients.
Butter. Melt on low heat. Add to other ingredients.
Press any key to close.

```

### Reading from and writing to a .txt file

Let's start by uncommenting the last line of our commented out code, the method call to write to a filepath:

```c#
public void Run(string filePath)
{
    var allRecipes = _recipesRepository.Read(filePath);
    _recipesUserInteraction.PrintExistingRecipes(allRecipes);

    _recipesUserInteraction.PromptToCreateRecipe();
    
    var ingredients = _recipesUserInteraction.ReadIngredientsFromUser();
    
    if (ingredients.Count() > 0)
    {
        var recipe = new Recipe(ingredients);
        allRecipes.Add(recipe);
        _recipesRepository.Write(filePath, allRecipes);
    
        _recipesUserInteraction.ShowMessage("Recipe added:");
        _recipesUserInteraction.ShowMessage(recipe.ToString());
    }
    else
    {
        _recipesUserInteraction.ShowMessage("No ingredients have been selected. " +
                                                   "Recipe Will not be saved");
    }

    _recipesUserInteraction.Exit();
}
```

Then implementing the method in our interface:

```C#
public interface IRecipesRepository
{
    List<Recipe> Read(string filePath);
    void Write(string filePath, List<Recipe> allRecipes);
}
```

Then implementing the missing interface members in our class:

```C#
public class RecipesRepository : IRecipesRepository
{
    public List<Recipe> Read(string filePath)
    {
        return new List<Recipe>
        {
            new Recipe(new List<Ingredient>
            {
                new WheatFlour(),
                new Butter(),
                new Sugar()
            }),
            new Recipe(new List<Ingredient>
            {
                new CocoaPowder(),
                new SpeltFlour(),
                new Cinnamon(),
            })
        };
    }

    public void Write(string filePath, List<Recipe> allRecipes)
    {
        throw new NotImplementedException();
    }
}
```

Let's add a new class to handle writing strings to and from a text file:

```C#
class StringsTextualRepository
{
    private static readonly string Separator = Environment.NewLine;

    public List<string> Read(string filePath)
    {
        var fileContents = File.ReadAllText(filePath);
        return fileContents.Split(Separator).ToList();
    }

    public void Write(string filePath, List<string> strings)
    {
        File.WriteAllText(filePath, string.Join(Separator, strings));
    }
}
```

We should add this class as a dependency to our repository class; But to follow the dependency inversion principle,
it should actually implement its interface. So first we should create an interface of this new class, we can have the
IDE do this for us by selecting it and selecting generate interface:

```C#
internal interface IStringsRepository
{
    List<string> Read(string filePath);
    void Write(string filePath, List<string> strings);
}
```

Then use dependency injection to add it to our recipes' repository class.   
Add a private readonly field of type IStringsRepository, then add a constructor passing in the strings repository
(note you should make your IStringsRepository interface public):

```C#
public class RecipesRepository : IRecipesRepository
{
    private readonly IStringsRepository _stringsRepository;

    public RecipesRepository(IStringsRepository stringsRepository)
    {
        _stringsRepository = stringsRepository;
    }
    
    public List<Recipe> Read(string filePath)
    {
        return new List<Recipe>
        {
            new Recipe(new List<Ingredient>
            {
                new WheatFlour(),
                new Butter(),
                new Sugar()
            }),
            new Recipe(new List<Ingredient>
            {
                new CocoaPowder(),
                new SpeltFlour(),
                new Cinnamon(),
            })
        };
    }

    public void Write(string filePath, List<Recipe> allRecipes)
    {
        throw new NotImplementedException();
    }
}
```

Finally, pass in the class into the method call :

```C#
var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(new StringsTextualRepository()),
    new RecipesConsoleUserInteraction(new IngredientsRegister()));
```

Now let's use it in our write method:

```C#
public void Write(string filePath, List<Recipe> allRecipes)
{
    _stringsRepository.Write(filePath, recipesAsStrings);
}
```

The method expects a list of strings as a parameter, each string will represent a recipe. We need to take our list of
recipes and convert them into strings with each string containing the ingredient IDs separated by a comma.

Lets implement that now:

```C#
public void Write(string filePath, List<Recipe> allRecipes)
{
    var recipesAsStrings = new List<string>();
    foreach (var recipe in allRecipes)
    {
        var allIds = new List<int>();
        foreach (var ingredient in recipe.Ingredients)
        {
            allIds.Add(ingredient.Id);
        }
        recipesAsStrings.Add(string.Join(",", allIds));
    }
    
    _stringsRepository.Write(filePath, recipesAsStrings);
}
```

We should now be able to run the app and test this. After running, entering a recipe and then closing the app; you
should navigate to the bin folder within your project, where you should find a recipes.txt file whose contents
look something like this:

```plain text
1,3,5
8,2,7
1,3,6
```

Now we have a text file containing recipes, we can implement reading from a text file and remove the hard coded recipe
we added here:

```c#
public List<Recipe> Read(string filePath)
{
    return new List<Recipe>
    {
        new Recipe(new List<Ingredient>
        {
            new WheatFlour(),
            new Butter(),
            new Sugar()
        }),
        new Recipe(new List<Ingredient>
        {
            new CocoaPowder(),
            new SpeltFlour(),
            new Cinnamon(),
        })
    };
}
```

Update the logic, once again making use of the strings repository:

```C#
public List<Recipe> Read(string filePath)
{
    var recipesFromFile = _stringsRepository.Read(filePath);
    var recipes = new List<Recipe>();

    foreach (var recipeFromFile in recipesFromFile)
    {
        var recipe = RecipeFromString(recipeFromFile);
        recipes.Add(recipe);
    }

    return recipes;
}
```

We have included a call to a method called RecipeFromString which we have not yet implemented, so have the IDE do this:

```C#
private Recipe RecipeFromString(string recipeFromFile)
{
    throw new NotImplementedException();
}
```

Then add the logic:

```C#
private Recipe RecipeFromString(string recipeFromFile)
{
    var textualIds = recipeFromFile.Split(",");
    var ingredients = new List<Ingredient>();

    foreach (var textualId in textualIds)
    {
        var id = int.Parse(textualId);
        //?
    }
    
    return new Recipe(ingredients);
}
```

We have now used the same separator of a comma in both this method and the write method, so we should add this as a
const variable in our class:

```C#
public class RecipesRepository : IRecipesRepository
{
    private readonly IStringsRepository _stringsRepository;
    private const string Seperator = ",";

    public RecipesRepository(IStringsRepository stringsRepository)
    {
        _stringsRepository = stringsRepository;
    }
    
    public List<Recipe> Read(string filePath)
    {
        var recipesFromFile = _stringsRepository.Read(filePath);
        var recipes = new List<Recipe>();

        foreach (var recipeFromFile in recipesFromFile)
        {
            var recipe = RecipeFromString(recipeFromFile);
            recipes.Add(recipe);
        }

        return recipes;
    }

    private Recipe RecipeFromString(string recipeFromFile)
    {
        var textualIds = recipeFromFile.Split(Seperator);
        var ingredients = new List<Ingredient>();

        foreach (var textualId in textualIds)
        {
            var id = int.Parse(textualId);
            //?
        }
        
        return new Recipe(ingredients);
    }

    public void Write(string filePath, List<Recipe> allRecipes)
    {
        var recipesAsStrings = new List<string>();
        foreach (var recipe in allRecipes)
        {
            var allIds = new List<int>();
            foreach (var ingredient in recipe.Ingredients)
            {
                allIds.Add(ingredient.Id);
            }
            recipesAsStrings.Add(string.Join(Seperator, allIds));
        }
        
        _stringsRepository.Write(filePath, recipesAsStrings);
    }
}
```

Now we need to find an ingredient by its ID. We already have a method that does this in out IngredientsRegister class,
so lets add it here as a dependency:

```C#
public class RecipesRepository : IRecipesRepository
{
    private readonly IStringsRepository _stringsRepository;
    private readonly IngredientsRegister _ingredientsRegister;
    private const string Seperator = ",";

    public RecipesRepository(IStringsRepository stringsRepository, IngredientsRegister ingredientsRegister)
    {
        _stringsRepository = stringsRepository;
        _ingredientsRegister = ingredientsRegister;
    }
```

Looking at it, we are implementing a concrete class here (IngredientsRegister) and not an abstraction. As this class is
really just a container for our ingredients, and does not contain any logic specified in the spec, it would probably
be fine to leave this as is. But to follow the dependency inversion principle and to account for any changes that
may happen in the future, lets create an interface for this class.

Generate the interface:

```C#
public interface IIngredientsRegister
{
    IEnumerable<Ingredient> All { get; }
    Ingredient GetById(int id);
}
```

Then search for where we are injecting this class and change the type to IIngredientsRegister.

Now we can finish implementing the RecipeFromString function:

```C#
private Recipe RecipeFromString(string recipeFromFile)
{
    var textualIds = recipeFromFile.Split(Seperator);
    var ingredients = new List<Ingredient>();

    foreach (var textualId in textualIds)
    {
        var id = int.Parse(textualId);
        var ingredient = _ingredientsRegister.GetById(id);
        ingredients.Add(ingredient);
    }
    
    return new Recipe(ingredients);
}
```

Before we can test this, we need to update the method calls with the IngredientsRegister as a parameter:

```C#
var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(new StringsTextualRepository(), new IngredientsRegister()),
    new RecipesConsoleUserInteraction(new IngredientsRegister()));
```

We are now passing in the IngredientsRegister class twice, so let's move it into a variable:

```C#
var ingredientsRegister = new IngredientsRegister();

var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(new StringsTextualRepository(), ingredientsRegister),
    new RecipesConsoleUserInteraction(ingredientsRegister));
```

Now if we delete the recipes.txt file and run the application, we will receive an error due to not finding the file.
So lets update the StringsTextualRepository to check if the file exists before attempting to read from it, and
if it does not exist to create a new empty list:

```C#
class StringsTextualRepository : IStringsRepository
{
    private static readonly string Separator = Environment.NewLine;

    public List<string> Read(string filePath)
    {
        if (File.Exists(filePath))
        {
            var fileContents = File.ReadAllText(filePath);
            return fileContents.Split(Separator).ToList();
        }
        return new List<string>();
    }

    public void Write(string filePath, List<string> strings)
    {
        File.WriteAllText(filePath, string.Join(Separator, strings));
    }
}
```

Run the app now, and it should work as intended, re-running should display the recipe from the previous time it was run.

### Reading and writing from a .json file

Saving to a .json file will be very similar to saving to a .txt file, except instead of saving the recipes as a comma
separated list, we will save them as a JSON array.

We have no need to change our RecipesRepository class. This class injects an IStringsRepository class to handle
reading and writing to a file. What format that file and text take does not matter to it.

To make our application support both .txt and.json files, We will create a new class that implements the
IStringsRepository interface.

This class will be very similar to StringsTextualRepository, so let's start by copying that class and renaming it to
StringsJsonRepository:

```C#
class StringsJsonRepository : IStringsRepository
{
    private static readonly string Separator = Environment.NewLine;

    public List<string> Read(string filePath)
    {
        if (File.Exists(filePath))
        {
            var fileContents = File.ReadAllText(filePath);
            return fileContents.Split(Separator).ToList();
        }
        return new List<string>();
    }

    public void Write(string filePath, List<string> strings)
    {
        File.WriteAllText(filePath, string.Join(Separator, strings));
    }
}
```

We need to change this line:

```C#
return fileContents.Split(Separator).ToList();
```

Here we will deserialize the data using the data serializer class:

```C#
return JsonSerializer.Deserialize<List<string>>(fileContents);
```

Another difference will be here:

```C#
File.WriteAllText(filePath, string.Join(Separator, strings));
```

Instead of joining the strings with a separator, we will serialize it to JSON:

```C#
File.WriteAllText(filePath, JsonSerializer.Serialize(strings));
```

We will no longer need the separator field as it's not being used, so this can be removed.   
The finished class will look like this:

```C#
class StringsJsonRepository : IStringsRepository
{
    public List<string> Read(string filePath)
    {
        if (File.Exists(filePath))
        {
            var fileContents = File.ReadAllText(filePath);
            return JsonSerializer.Deserialize<List<string>>(fileContents);
        }
        return new List<string>();
    }

    public void Write(string filePath, List<string> strings)
    {
        File.WriteAllText(filePath, JsonSerializer.Serialize(strings));
    }
}
```

Now we need to change how RecipesRepository is created.   
Instead of passing in a StringsTextualRepository object, pass in a StringsJsonRepository.   
Change the name of the file from recipes.txt to recipes.json:

```C#
var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(new StringsJsonRepository(), ingredientsRegister),
    new RecipesConsoleUserInteraction(ingredientsRegister));

cookiesRecipesApp.Run("recipes.json");
```

You should now be able to run the app as normal.   
If you create a recipe and then check the recipes.json file, you should see something like:

```json
["4,6,1"]
```

The spec for this application stated that weather a .txt or .json file is used should be controlled by a const
variable in the program. We could create a variable called something like isJson. However, this is not very expressive,
it does not tell us what the other format is, and it does not allow the app to expand to except more than two formats.
For these reasons, we will create an enum to handle this.

```c#
public enum FileFormat
{
    Json,
    Txt
}
```

And then create a const value of this enum, and set its value to Json:

```C#
const FileFormat fileFormat = FileFormat.Json;
```

Now we will use this to set the type of repository object we create:

```C#
IStringsRepository stringsRepository = fileFormat == FileFormat.Json ?
    new StringsJsonRepository() : 
    new StringsTextualRepository();
```

Now we need to handle the file path:

```C#
const string fileName = "recipes";
var filePath = fileFormat == FileFormat.Json ? 
    $"{fileName}.json" : 
    $"{fileName}.txt";
```

So the beginning of our app will look like this:

```C#
const FileFormat fileFormat = FileFormat.Json;

IStringsRepository stringsRepository = fileFormat == FileFormat.Json ?
    new StringsJsonRepository() : 
    new StringsTextualRepository();

const string fileName = "recipes";
var filePath = fileFormat == FileFormat.Json ? 
    $"{fileName}.json" : 
    $"{fileName}.txt";

var ingredientsRegister = new IngredientsRegister();

var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(stringsRepository, ingredientsRegister),
    new RecipesConsoleUserInteraction(ingredientsRegister));

cookiesRecipesApp.Run(filePath);

public enum FileFormat
{
    Json,
    Txt
}
```

Although this works, there should be a dedicated class that builds the path from the files name and the fileFormat
enum.   
Let's call the class FileMetadata, as calling it FileData could confuse someone that it is about the data in
the file.

```C#
public class FileMetadata
{
    public string Name { get; }
    public FileFormat FileFormat { get; }

    public FileMetadata(string name, FileFormat fileFormat)
    {
        Name = name;
        FileFormat = fileFormat;
    }

    public string ToPath() => $"{Name}.{FileFormat.AsFileExtension()}";
}
```

This method simply takes a filename and file format and uses them to create a file path. But is should not be this
classes responsability to know how to convert a file format into an extension, that should really be the job
of the enum. To implement that we can create an extension method on the enum, which is what we have called above
FileFormat.AsFileExtension().   
Let's create that extension method now:

```C#
public static class FileFormatExtensions
{
    public static string AsFileExtension(this FileFormat fileFormat) =>
        fileFormat == FileFormat.Json ? "json" : "txt";
}
```

Next let's create a fileMetaData object:

```C#
var fileMetadata = new FileMetadata(fileName, fileFormat);
```

And pass it as a parameter:

```C#
cookiesRecipesApp.Run(fileMetadata.ToPath());
```

The updated beginning of our app should now look like this:

```C#
const FileFormat fileFormat = FileFormat.Json;

IStringsRepository stringsRepository = fileFormat == FileFormat.Json ?
    new StringsJsonRepository() : 
    new StringsTextualRepository();

const string fileName = "recipes";
var fileMetadata = new FileMetadata(fileName, fileFormat);

var ingredientsRegister = new IngredientsRegister();

var cookiesRecipesApp = new CookiesRecipesApp(
    new RecipesRepository(stringsRepository, ingredientsRegister),
    new RecipesConsoleUserInteraction(ingredientsRegister));

cookiesRecipesApp.Run(fileMetadata.ToPath());

```

The app should now work as it did before, with the ability to change the file format by changing this line:

```C#
const FileFormat fileFormat = FileFormat.Json;
```

### Template method design pattern

We currently have two classes that implement the IStringsRepository method that are almost identical:

```C#
class StringsTextualRepository : IStringsRepository
{
    private static readonly string Separator = Environment.NewLine;

    public List<string> Read(string filePath)
    {
        if (File.Exists(filePath))
        {
            var fileContents = File.ReadAllText(filePath);
            return fileContents.Split(Separator).ToList();
        }
        return new List<string>();
    }

    public void Write(string filePath, List<string> strings)
    {
        File.WriteAllText(filePath, string.Join(Separator, strings));
    }
}

class StringsJsonRepository : IStringsRepository
{
    public List<string> Read(string filePath)
    {
        if (File.Exists(filePath))
        {
            var fileContents = File.ReadAllText(filePath);
            return JsonSerializer.Deserialize<List<string>>(fileContents);
        }
        return new List<string>();
    }

    public void Write(string filePath, List<string> strings)
    {
        File.WriteAllText(filePath, JsonSerializer.Serialize(strings));
    }
}
```

To avoid this code duplication, we will create an abstract base class with the same methods of the derived classes,
in place of the two lines that are different we would call abstract methods. This is called the template method design
pattern.

Start by copying one of the classes then making it abstract and public and rename it to StringsRepository:

```C#
public abstract class StringsRepository : IStringsRepository
{
    private static readonly string Separator = Environment.NewLine;

    public List<string> Read(string filePath)
    {
        if (File.Exists(filePath))
        {
            var fileContents = File.ReadAllText(filePath);
            return JsonSerializer.Deserialize<List<string>>(fileContents);
        }
        return new List<string>();
    }

    public void Write(string filePath, List<string> strings)
    {
        File.WriteAllText(filePath, string.Join(Separator, strings));
    }
}
```

Next lets take the first line of differing code:

```C#
return JsonSerializer.Deserialize<List<string>>(fileContents);
```

And change this to call a new abstract method which we will call TextToStrings:

```C#
return TextToStrings(fileContents);
```

Generate the method and make it protected and abstract:

```C#
protected abstract List<string> TextToStrings(string fileContents);
```

The two derived classes will improve their own versions of this method, it needs to be protected so the derived
classes can see it.

Lets do the same with the other line of code that differs between our current classes:

```C#
File.WriteAllText(filePath, string.Join(Separator, strings));
```

Let's here call a method called StringToText:

```C#
File.WriteAllText(filePath, StringsToText(strings));
```

And implement an abstract method for it:

```C#
protected abstract string? StringsToText(List<string> strings);
```

We should also remove the Separator field as it was specific to one of the classes:

```C#
private static readonly string Separator = Environment.NewLine;
```

Finished abstract class should look like this:

```C#
public abstract class StringsRepository : IStringsRepository
{
    public List<string> Read(string filePath)
    {
        if (File.Exists(filePath))
        {
            var fileContents = File.ReadAllText(filePath);
            return TextToStrings(fileContents);
        }
        return new List<string>();
    }

    protected abstract List<string> TextToStrings(string fileContents);

    public void Write(string filePath, List<string> strings)
    {
        File.WriteAllText(filePath, StringsToText(strings));
    }

    protected abstract string? StringsToText(List<string> strings);
}
```

Now, in our two derived classes have them implement StringsRepository instead of IStringsRepository (note: they still
implement IStringsRepository it is just now via StringsRepository). Have the IDE implement the abstract methods, then
copy the sections of code which we replaced with method calls and add them as return statements in the appropriate
methods. The read and write methods can then be deleted as these will be inherited from the base class:

```C#
public class StringsTextualRepository : StringsRepository
{
    private static readonly string Separator = Environment.NewLine;

    protected override List<string> TextToStrings(string fileContents)
    {
        return fileContents.Split(Separator).ToList();
    }
    
    protected override string? StringsToText(List<string> strings)
    {
        return string.Join(Separator, strings);
    }
}

public class StringsJsonRepository : StringsRepository
{

    protected override List<string> TextToStrings(string fileContents)
    {
        return JsonSerializer.Deserialize<List<string>>(fileContents);
    }
    
    protected override string? StringsToText(List<string> strings)
    {
        return JsonSerializer.Serialize(strings);
    }
}
```

### Cleanup and project organizing

All of our classes should have their own file, so let's move them now. Let's start with the files related to
IStringsRepository. Create a new folder in the project called DataAccess, then select each class name in turn and
select the appropriate option to move them into their own file. Next, drag each of these class files into the DataAccess
folder.

Next, let's move the classes related to file access, create a new folder called FileAccess and move the FileFormat enum,
the FileFormatExtensions class and the FileMetaData class into the folder in separate files.

next move the classes related to recipes into the Recipes folder we already have, and the two ingredient
classes into the Ingredients folder within the Recipes folder.

Next make a namespace (folder) called App and move the CookiesRecipesApp, IRecipesUserInteraction, and the
IRecipesConsoleUserInteraction classes to separate files within it.

Note:
: Be sure to check that the classes namespaces have updated correctly when they are moved to reflect their new location.