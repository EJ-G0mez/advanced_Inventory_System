# Welcome to the Advanced Inventory System

## This project is a practice program attempting to model an Inventory System of a video game using **Unreal Engine 5.**

The Inventory Systems main goal is to serve as both a _practice and training exercise_ for myself and as a guide for other that wish to implement something similar into their games/projects. This Inventory includes the following features:
* Stackable and Usable Items
* Chests and Contatiners with their own inventory.
* Transfering of items between slots and container inventroies
* Dropping items individually or in stack, the stack generates a "Loot Bag" with their own inventory.
* A hot bar in which you can drag items to Bind them to the hotbar slot
* An attempt of a save/loading system in which your inventory is saved at the end of a Game and loads the inventory at the beginning of the game
* An attempt of a save/load system where it saves the position of dropped items.

This document is meant to be a documentation of all the features as well as an explanation using Pseudo code for all the added elements of the project.

# Content

<details>

  <summary>Syntaxis, Diagrams and Media</summary>

  # Syntaxis, Diagram and Media

  ## How is this document organized and how should you read it?
  
  This section is to give an understanding of how the document will be formated and organized as well as explaining the diagrams that will be placed around this document.

  ### Syntax

  All of the added or edited functions, structures or events will be added to this document. Any default classes, structures, etc, that are in the base Unreal Engine default projects, will only be added if they are specifically mentioned in the context of the coding.

  The coding will be done in a pseudocode that will ressemble as much as possible a object oriented language

  Example:

  ```
    class newClass {

      public  bool attribute;
      public  int attribute2;
      private static  struct attribute3;

      public function1( int parameter, bool parameter){
        return returnType;
      }

      private functionEvent(){
        attribute += 1;
      }
    }
  ```
  
  ### Diagrams 
  
  There will be general UML Diagrams summarizing and showing the logic of every component, as Unreal has very specific color coding for their nodes (i.e, events, functions, and the specific color of the data structures) these diagrams will use the colors used in Unreal for the classes and data structures.
  
  Example:

  ![imagen](https://github.com/user-attachments/assets/c4eb07d7-86af-498c-9c1a-d473067e88b4) [^1]

  [^1]: Example of a diagram that will be used in this document

### Media

Media in this document will have foot notes describing what the media is showing. Media can include images, videos of the project to assist in the understanding of this project.

Example:

  ![dotboxspawnemitter](https://github.com/user-attachments/assets/a0b89f3c-2c57-42c8-b502-7a0211c958d8)[^2]

  [^2]: Example of an image showing an Unreal Project taken from the [Unreal Documentation Webpage](https://dev.epicgames.com/documentation/en-us/unreal-engine/nodes-in-unreal-engine). 
  
</details>

<details>
  
  <summary>Inventory System</summary>

  # Inventory_System

  ## The main function of the system

  This function is the core of this project, it is the system in which items are added, removed, transfered, used, etc.

  ![imagen](https://github.com/user-attachments/assets/32e7b6d2-2cfd-4077-83d2-09ac555ea428) [^3]
  
  [^3]: Inventory System Class Diagram

  ### Project Settings

  For this structure you will need to add an input into your Project settings to Interact with items, in this case we will use the "i" key to give the ability to interact with pickable items
  ![imagen](https://github.com/user-attachments/assets/b75ad283-0a00-4999-af1e-020f104a5118) [^4]

  [^4]: Image showing the project setting where in Inputs, we add the Interact Input that is set to the "i" key.

  ```
  #import F_Slot_Struct
  #import W_Display_Message
  #import SaveData-Level;
  #import Loot_Bag

  public class Inventory System {
    private int inventorySize = 16 
    private F_Slot_Struct[] content;
    private float interactionTrace = 300.0;
    private Actor lookAtActor;
    private W_Display_Message displayMessage;
  }
  ```

  <summary>Event Graph</summary>

  ### Event Graph

  This section show every single event in the Inventory System blueprint class.

  <ins>Event Begin Play</ins>

  The Begin Play event starts at the beginning of the project. It is able to load any previous saved inventories, generate the inventory size, add a display message to the viewport when an item is interactable, and auto saves the inventory. 

  ![imagen](https://github.com/user-attachments/assets/883fade5-97d6-4d10-bf7f-b4d5e6b991de)[^5]

  [^5]: Begin Play Event in Unreal Engine 5

  ```
  public class Inventory System {

    private int inventorySize = 16 
    private F_Slot_Struct[] content;
    private float interactionTrace = 300.0;
    private Actor lookAtActor;
    private W_Display_Message displayMessage;

    private eventDispatch On_Inventory_Update; // Event Dispatcher, allows multiple events to happens at once

    // starts at the beginning of the project
    public event Event_Begin_Play(){
      loadInventory(self);
      content.resize(inventorySize); //sets the contents array size to 16
      displayMessage = createWidget(classtype.W_Display_Message);
      addToViewport(displayMessage);
      bindEventTo(On_Inventory_Update, AutoSave_Inventory);
    }
  }
  ```

  <ins>Event Tick</ins>

  This Event updates every frame and checks the interaction trace to see if there is an interactable item in front of the player.

  ![imagen](https://github.com/user-attachments/assets/82d3271a-62f8-43c7-8dd1-2f07f0346b82) [^6]

  [^6]: Tick Event in Unreal Engine 5

  
  ```
   public event Tick(){
    interactionTrace(self); //Check if an interactive item is currently in the players interactive distance
   }
  ```

  <ins>Event InputAction Interact</ins>

  This Event triggers every time the player interacts with an item with the Inpu "I".

  ![imagen](https://github.com/user-attachments/assets/06a68e27-e3be-416e-9f26-68112ac35407) [^7]

  [^7]: InputAction Interact Event in Unreal Engine 5

  ```
   public event InputAction_Interact(){ 
    switch(input Interact){ //checks if the input is pressed or released
        case "pressed":
          if(lookAtActor.isValid){
            Server_Interact(self, lookAtActor)
          }
      }
   }
  ```

 <ins>Event Server_Interact</ins>

 This is a very specific event, it checks if a player is interacting from server or client side with an object, if the object is on server side it will update as so, if it's from client, it will declare the object as client side and set the owner as client.

 ![imagen](https://github.com/user-attachments/assets/74e047bc-201f-4633-9280-847860b07435) [^8]

  [^8]: Server_Interact Details in Unreal Engine 5

 ![imagen](https://github.com/user-attachments/assets/daba19c7-25b6-48fe-b60e-af8fd5c79e76) [^9]

 [^9]: Server_Interact Event in Unreal Engine 5

 ```
  public event Server_Interact(actor){
    BP_ThirdPersonCharacter owner = (BP_ThirdPersonCharacter) self.getOwner; //We get the owner of the player and cast the value as BP_ThirdPersonCharacter type Objecct
    if(target.getComponentByClass(Item_Data_Component).isValid){
      Interact_With(target, owner); //This will go to the Interact with Event algonside with the target object and the owner  
    } else {
      setOwner(target, owner.getController); // This sets the client player controller as owner of the object
      OnLocalInteract(self, target, owner);
    }
  }
 ```

<ins>Event MC_Update</ins>

It simply updates any inventory changes

![imagen](https://github.com/user-attachments/assets/bed2a15e-f858-42c9-94a7-a7128949bf6f) [^10]

[^10]: MC_Update Event in Unreal Engine 5

```
  public event MC_Update(){
    On_Inventory_Update.callOn(self);
  }
```

<ins>Event "I"</ins>

A simple debugging event that triggers everytime "I" is pressed.

![imagen](https://github.com/user-attachments/assets/fb6627fe-2f49-49e9-b597-0f569f7ccc5a) [^11]

[^11]: "I" Event in Unreal Engine 5

```
  public event I(){
    DEBUGPrintContent();
  }
```

<ins>Event Server_Transfer_Slots</ins>

This Event updates when there is a transfer in the inventory slots of one or multiple inventories.

![imagen](https://github.com/user-attachments/assets/ae0f49cd-2681-4823-8d3a-d82089881db4) [^11]

[^11]: Server_Transfer_Slots Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/aee8b24f-758e-40bb-9568-00fd2f524a20) [^12]

[^12]: Server_Transfer_Slots Event in Unreal Engine 5

```
  public event Server_Transfer_Slots(int sourceIndex, Inventory_System sourceInventory,int destinationIndes){
    transferSlots(self, sourceIndex, spurceInvenotry, destinationIndex);
  }
```

<ins>Event OnLocalInteract</ins>

This Event triggers when the player locally interacts with the object

![imagen](https://github.com/user-attachments/assets/c450e0e0-c2c7-4ce5-87b5-a31151a0e44d) [^13]

[^13]: OnLocalInteract Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/2600df71-df9f-41fe-ae80-1e118977a99a) [^14]

[^14]: Server_Transfer_Slots Event in Unreal Engine 5

```
  public event OnLocalInteract(actor targetActor, actor interactor){
    interactWith(targetActor, (BP_Third_Person_Character) interactor);
  }
```

<ins>Event Server_Remove</ins>

This Event triggers when the player removes an item from their inventory

![imagen](https://github.com/user-attachments/assets/ff1d9ff9-c840-4126-8ceb-34284712acfc) [^15]

[^15]: Server_Remove Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/62c430bf-8abf-41ba-8c24-6d2468d6a19e) [^16]

[^16]: Server_Transfer_Slots Event in Unreal Engine 5

```
  public event Server_Remove(int index, bool removeWholeStak, bool isConsumed){
    removeFromInventory(index, removeWholeStask, isConsumed);
  }
```

<ins>Event Server_Drop_Item</ins>

This Event triggers when the player drops an item, for both server and client side, this also saves where these dropped items are placed so they are loaded in the next load of the game

![imagen](https://github.com/user-attachments/assets/a42db1fd-c758-43cd-b7d4-b5a4ed8578b4) [^17]

[^17]: Server_Drop_Items Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/21da8828-176c-44e2-be95-6b1f3b8bfa1b) [^18]

[^18]: Server_Drop_Item Event in Unreal Engine 5

```
  public event Server_Drop_Item(name itemID, int quantity){
    if(quantity > 1){
      addToInventory(spawnActor("Class=" classType.Loot_Bag,"SpawnTransformLocation=" getDropLocation()).Inventory_System, itemID, quantity); // This will generate a loot bag with the amount of items that were dropped
    } else {
      actor droppedActor = spawnActor("Class=" getItemData.itemClass, "SpawnTransformLocation=" getDropLocation()); //it will create a new actor with the item characteristics and position where it was last placed
      delay(.2); // This delay is added to allow the game to save apporpirately with entering a nullPointerException
      Save_Data_Level saveData = ((BPThirdPersonGameMode) getGameMode()).Save_Data_Level; //We have the save data of Game
      add(saveData.actorAdded, (soft reference) getItemData.itemClass, getActorTransform(droppedActor)); //we add the dropped items to the save data
      saveGameToSlot(saveData, ((BPThirdPersonGameMode) getGameMode()).levelDataSlot); //we save the game in a save file
      DEBUGPrintContent();
    }
  }
```

<ins>Event Server_Consume_Item</ins>

This Event triggers when the player consumes an item and it gives them an effect on server and client side

![imagen](https://github.com/user-attachments/assets/a42db1fd-c758-43cd-b7d4-b5a4ed8578b4) [^19]

[^19]: Server_Consume_Item Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/496cf6bf-bbc8-49c3-9ed7-bab2e3eff8df) [^20]

[^20]: ServerConsume_Item Event in Unreal Engine 5

```
  public event Server_Drop_Item(name itemID){
    spawnActor("Class=" getItemData(itemID).itemEffect, "SpawnTransfromLocation"= getDropLocation());
  }
```

<ins>Event AutoSave_Inventory</ins>

This Event triggers everytime the player loads the game, it saves to teh current save file

![imagen](https://github.com/user-attachments/assets/4b9750c2-9abe-4894-a112-52e3c8649232) [^21]

[^21]: AutoSave_Inventory Event in Unreal Engine 5

```
  public event AutoSave_Inventory(){
    saveInventory();
  }
```
  
<summary>AddToInventory</summary>

This is a function that allows the player to add items to their inventory.

![imagen](https://github.com/user-attachments/assets/9eb9240e-4f3f-4d46-862a-92913472c7c8) [^22]

[^22]: addtoInventory function in Unreal Engine 5

```
public addToInventory(name itemID, int quantity){
  boolean localHasFailed;
  int localQuantityRemaining = quantity;
  while(localQuantityReaming > 0 AND !localHasFailed){
    if(findSlot(itemID).foundSlot){
      addToStack(findSlot(itemID).index, 1);
      localQuantityRemaining--;
    } else {
      if(anyEmptySlotsAvailable().hasEmptySlot){
        if(createNewStack(itemID, 1).success){
          localQuantityRemaining--;
        } else {
          localHasFailed = true;
        }
      }else {
        localHasFailed = true;
      }
    }
  }
  callOnInventoryUpdate();
  return success = !localHasFailed, quantityReaming = localQuantityReaming;
}
```
  
</details>




