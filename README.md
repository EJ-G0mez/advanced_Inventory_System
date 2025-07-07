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

 ![imagen](https://github.com/user-attachments/assets/3aceb936-677c-483e-8894-e7a6ea9ac396) [^3]
  
  [^3]: Inventory System Class Diagram

  ### Project Settings

  For this structure you will need to add an input into your Project settings to Interact with items, in this case we will use the "i" key to give the ability to interact with pickable items
  ![imagen](https://github.com/user-attachments/assets/b75ad283-0a00-4999-af1e-020f104a5118) [^4]

  [^4]: Image showing the project setting where in Inputs, we add the Interact Input that is set to the "i" key.

  ```
  #import F_Slot_Struct
  #import W_Display_Message
  #import SaveData-Level
  #import Loot_Bag
  #import Item_Data

  public class Inventory System {
    private int inventorySize = 16 
    private F_Slot_Struct[] content;
    private float interactionTrace = 300.0;
    private Actor lookAtActor;
    private W_Display_Message displayMessage;
    ...

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
      LoadInventory(self);
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
    TransferSlots(self, sourceIndex, spurceInvenotry, destinationIndex);
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
    RemoveFromInventory(index, removeWholeStask, isConsumed);
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
      AddToInventory(spawnActor(/*Class=*/ classType.Loot_Bag,/*SpawnTransformLocation=*/ getDropLocation()).Inventory_System, itemID, quantity); // This will generate a loot bag with the amount of items that were dropped
    } else {
      actor droppedActor = spawnActor(/*Class=*/ getItemData.itemClass, /*SpawnTransformLocation=*/ getDropLocation()); //it will create a new actor with the item characteristics and position where it was last placed
      delay(.2); // This delay is added to allow the game to save apporpirately with entering a nullPointerException
      Save_Data_Level saveData = ((BPThirdPersonGameMode) getGameMode()).Save_Data_Level; //We have the save data of Game
      add(saveData.actorAdded, (soft reference) getItemData.itemClass, getActorTransform(droppedActor)); //we add the dropped items to the save data distionary
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
    SaveInventory();
  }
```
  
<summary>AddToInventory</summary>

This is a function that allows the player to add items to their inventory.

![imagen](https://github.com/user-attachments/assets/9eb9240e-4f3f-4d46-862a-92913472c7c8) [^22]

[^22]: addtoInventory function in Unreal Engine 5

```
public AddToInventory(name itemID, int quantity){
  boolean localHasFailed;
  int localQuantityRemaining = quantity;
  while(localQuantityReaming > 0 AND !localHasFailed){
    if(FindSlot(itemID).foundSlot){
      AddToStack(findSlot(itemID).index, 1);
      localQuantityRemaining--;
    } else {
      if(AnyEmptySlotsAvailable().hasEmptySlot){
        if(CreateNewStack(itemID, 1).success){
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
  return boolean success = !localHasFailed, int quantityReaming = localQuantityReaming;
}
```

<summary>RemoveFromInventory</summary>

This is a function that allows the player to remove items froms their inventory.

![imagen](https://github.com/user-attachments/assets/56e0d81b-a76b-4a77-b256-c75e86873c83) [^23]

[^23]: removeFromInventory function in Unreal Engine 5

```
public RemoveFromInventory(int index, boolean removeFromWholeStack, isConsumed){
  int localQuantity = content.itemName;
  name localItem = content. quantity;
  sequence {
    then 0:
      if(removeFromWholeStack OR localQuantity == 1){
        setArrayElem(content, index);
        if(!isConsumed){
          serverDropItem(localItem, localQuantity);
        }
      } else {
        setMembers(content[index],content[index].quantity-1);
        if(!isConsumed){
          serverDropItem(localItem, 1);
        }
      }
    then 1:
      MCUpdate();
  }
}
```

<summary>InteractionTrace</summary>

This is a function that detects when a player is the proximity of an interactable item, this is done so when the player is lookingat an item, the code will detect the item.

![imagen](https://github.com/user-attachments/assets/d18a760a-7cc8-4286-99a2-feb4972668f6) [^24]

[^24]: interactionTrace function in Unreal Engine 5

```
public InteractionTrace(){
  SphereTrace  sphereTrace = sphereTraceByChannel(/*start=*/(getActorLocation(getPlayerCharacter))-Vector3(0.0, 0.0, 65.0),
                                    /*end=*/ ((getActorLocation(getPlayerCharacter()))-Vector3(0.0, 0.0, 65.0) + (getActorForwardVector(getPlayerCharacter()) * interactionTrace)),
                                    /*radius=*/ 25.0,
                                    /*traceChannel=*/ "Interactive",
                                    /*traceComplex=*/ false,
                                    /*actorsToIgnore=*/makeArray(getOwner()),
                                    /*drawDebugType=*/ null,
                                    /*ignoreSelf=*/ true);
  if(sphereTrace.returnValue){
    if(sphereTrace.outHit.hitActor != lookAtActor){
      lookAtActor = sphereTrace.outHit.hitActor;
      showMessage(displayMessage, lookAt(lookAtActor));
    }
  } else {
    lookAtActor = null;
    showMessage(displayMessage, "");
  }
}
```

<summary>FindSlot</summary>

This is a function that searches for any slots that have items in the inventory.

![imagen](https://github.com/user-attachments/assets/038700db-7085-4114-b31e-fd9766aa9f7c) [^26]

[^26]: findSlot function in Unreal Engine 5

```
public FindSlot(name itemID){
  forEach x in content{
    if(content[x].itemName == itemID){
      if(content[x].quantity == itemID.GetMaxStackSize()){
        return int index = content[x].arrayIndex, boolean foundSlot = true;
      }
    }
  }
  return int index = -1, boolean foundSlot = false;
}
```

<summary>GetMaxStackSize</summary>

This is a function that checks if a slot has reached the maximum amount of the item in the slot;

![imagen](https://github.com/user-attachments/assets/032a6ee3-c843-4a89-a98e-04ff102da015) [^27]

[^27]: GetMaxStackSize function in Unreal Engine 5

```
public GetMaxStackSize(name itemID){
  switch(getDataTableRow("Data_Table=" Item_Data, itemID)){
    case "Found":
      return int stackSize = getDataTableRow("Data_Table=" Item_Data, itemID).stackSize;
    case "NotFound":
      return int stackSize = -1;
  }
}
```

<summary>AddToStack</summary>

This is a function that adds an item to a slot.

![imagen](https://github.com/user-attachments/assets/8d03f842-b4d7-4993-9040-491153088680) [^28]

[^28]: AddtoStack function in Unreal Engine 5

```
public AddToStack(int index, int quantity){
  setArrayElem(content, index, makeF_Slot_Struct(content[index].itemName, content[index].quantity + quantity));
}
```

<summary>AnyEmptySlotsAvailable</summary>

This is a function that checks if the player has any empty slots in their inventory.

![imagen](https://github.com/user-attachments/assets/71d05f4d-30f4-4ca2-a83f-8307a92be273) [^29]

[^29]: AnyEmptySlotsAvailable function in Unreal Engine 5

```
public AnyEmptySlotsAvailable(){
  forEach x in content{
    if (content[x].quantity == 0){
      return boolean hasEmpltySlot = true, int emptyIndex = x;
    }
  }
  return boolean hasEmpltySlot = false, int emptyIndex = -1;
}
```

<summary>CreateNewStack</summary>

This is a function that creates a new stack

![imagen](https://github.com/user-attachments/assets/4fea3ece-ddf2-414d-8203-28a046b2ddab) [^30]

[^30]: CreateNewStack function in Unreal Engine 5

```
public CreateNewStack(name itemID, int quantity){
  if(AnySlotsAvailable().hasEmptySlot){
    setArrayElem(content, AnyEmptySlotsAvailable().emptyIndex, makeF_Slot_Struvt(itemID, quantity));
    return boolean success = true;
  } else {
    return boolean success = false;
  }
}
```


<summary>DEBUGPrintContent</summary>

This functions' sole purpose is meant for troubleshooting, and has no defined code, overall it can be whatever purpose needed to be. althoug the output will always be a print function

```
public DEBUGPrintContent(){
  ...
  ...
  printString(...);
}
```


<summary>TransferSlots</summary>

This function allows the player to transfer content between slots, add items to slots that do not go over the maximum stack size, adn transfer items between inventories.

![imagen](https://github.com/user-attachments/assets/f6a56939-56e3-467a-b5dd-050a913ba990) [^31]

[^31]: TransferSlot function in Unreal Engine 5


```
public TransferSlots(int sourceIndex, Inventory_System sourceInventory, int destinationIndex){
  F_Slot_Struct localSlotContent = sourceIntventory.content[sourceindex];
  if((destinationIndex < 0) == false){
    //Check if there is a same item in the destination slot to add it without going the max stack size
    if(localSlotContent.itemName == content[destinationIndex].itemName){
      int addedQuantity = localSlotContent.quantity + content[destinationIndex].quantity;
      int maxStackSize = GetMaxStackSize(localSlotContent.itemName);
      //Source Index
      setArrayElem(sourceInventory.content,
                  sourceIndex,
                  select(/*False=*/ None, /*True=*/ localSlotContent.itemName, /*Index=*/ clamp(addedQuantity - maxStackSize, 0, maxStakSize) > 0),
                  clamp(addedQuantity - maxStackSize, 0, maxStakSize));
      //Destination index
      setArrayElem(content,
                  destinationIndex,
                  localSlotContent.itemName,
                  clamp(addedQuantity, 0, mazStackSize));
      MC_Update(self);
      MC_Update(sourceIndex);
    } else {
      //source Index
      setArrayElem(sourceInventory.content, sourceIndex, content[destination]);
      //destination Index
      setArrayElem(content, destinationIndex, loclaSlotContent);
      MC_Update(self);
      MC_Update(sourceInventory);
    }
  }
}
```

<summary>GetItemData</summary>

This function allows to get the item data of a specified item.

![imagen](https://github.com/user-attachments/assets/8678386c-a5dc-4746-a613-9527b3152b31) [^32]

[^32]: GetItemData function in Unreal Engine 5


```
public GetItemData(name itemID){
  return F_Item_Struct itemData = getDataTableRow(Item_Data, nameID);
}
```

<summary>GetDropLocation</summary>

This function allows to get the position in which a player drops an item.

![imagen](https://github.com/user-attachments/assets/a7e92f38-f9f3-4ebb-9469-b9adf8823664) [^33]

[^33]: GetDropLocation function in Unreal Engine 5


```
public GetDropLocation(){
  return Vector3 location = lineTraceByChannel( /*Start=*/ getActorLocation(getOwner()) + (randomUnitVectorinConeinDegrees(getActorForwardVector(getOwner), 30.0) * 150.0),
                                              /*End=*/ getActorLocation(getOwner()) + (randomUnitVectorinConeinDegrees(getActorForwardVector(getOwner), 30.0) * 150.0) - Vector3(0.0, 0.0, 500.0),
                                              /*Trace Channel=*/ "Visibility;
                                              /*Ignore Self=*/ true).location;
}
```

<summary>ConsumeItem</summary>

This function allows the player to consume an item if it is consumable.

![imagen](https://github.com/user-attachments/assets/d8906a64-b90e-4198-9c74-bfcc94339ca5) [^34]

[^34]: ConsumeItem function in Unreal Engine 5


```
public ConsumeItem(int index){
  name localItemID = content[index].itemName;
  int localQuantity = content[index].quantity;
  sequence {
    then "0":
      Server_Remove(self, index, false, true);
      Server_Consume_Item(self, localItemID);
    then "1":
      MC_Update():
  }
}
```

<summary>QueryInventory</summary>

This function allows for a search of a specific item in the inventory.

![imagen](https://github.com/user-attachments/assets/fc940594-296f-4205-b718-446e61acb3a2) [^35]

[^35]: QueryInventory function in Unreal Engine 5


```
public QueryInventory(name itemID, int quantity){
  int runningTotal = 0;
  forEach x in content{
    if(content[x].itemName == itemID){
      runningTotal += content[x].quantity;
    }
  }
  return int oldQuantity = runningTotal, boolean success = runningTotal >= quantity
}
```

<summary>GetQuantity</summary>

This function gets the total items in an inventory.

![imagen](https://github.com/user-attachments/assets/e02e806b-38b0-4bbb-a1d8-e350e5493fdc) [^36]

[^36]: GetQuantity function in Unreal Engine 5


```
public GetQuantity(){
  int localSum = 0;
  forEach x in content{
    localSum += content[x].quantity
  }
  return int total = localSum;
}
```

<summary>SaveInventory</summary>

This function allows the game to save all states of the current inventories in existance.

![imagen](https://github.com/user-attachments/assets/b0f48d69-f2ea-4b3b-a10c-461cb69f05ee) [^37]

[^37]: SaveInventory function in Unreal Engine 5


```
public SaveInventory(){
  if(getOwner() == getPlayerCharacter(0)){
    F_Slot_Struct inventoryContent[] = set(content, ((My_Game_Instance) getGameInstance()).SaveData-PlayerData);
    SaveGametoSlot(((My_Game_Instance) getGameInstance()).SaveData-PlayerData, "MyData", 0);
  } else {
    add(/* target array*/ ((My_Game_Instance) getGameInstance()).SaveData-PlayerData,/*invenotry system*/ self,/*item to be added*/ makeF_Container_Struct(content));
  }
}
```

<summary>LoadInventory</summary>

This function allows the game to load all saved inventories.

![imagen](https://github.com/user-attachments/assets/d71f4a07-27af-4dc6-8066-276b89ed6d69) [^38]

[^38]: LoadInventory function in Unreal Engine 5


```
public LoadInventory(){
  if(getOwner() == getPlayerCharacter(0)){
   content = ((My_Game_Instance) getGameInstance()).SaveData-PlayerData.inventoryContent
  } else {
    content = find(/* target array*/ ((My_Game_Instance) getGameInstance()).SaveData-PlayerData,/*invenotry system*/ self).contents;
  }
}
```

</details>

<details>

<summary>F_Item_Struct</summary>

# F_Item_Struct

## What's in an item

This is a structure that defines what data an item has, this allows us to alter these data and move items around without much issue;.

### Structure Elements

![imagen](https://github.com/user-attachments/assets/75ff2d91-a444-412c-ad1f-9366f9cacea0) [^39]

[^39]: F_Item_Struct class diagram

![imagen](https://github.com/user-attachments/assets/fc7f6703-36bb-4522-bfe8-31c6d8bf71e2) [^40]

[^40]: F_Item_Struct attributes in Unreal Engine 5

```
struct F_Itemt_Struct {
  text name,
  text description,
  Texture2D thumbnail,
  actorClass itemClass,
  int stackSize,
  BP_Item_Effect itemEffect,
  category E_Item_Category
}
```
</details>

<details>

<summary>E_Item_Category</summary>

# E_Item_Category

## What kind of item is this

This is an enumator that classifies the items to different categories.

### Enumator 

![imagen](https://github.com/user-attachments/assets/f170cdfd-7f6b-42a6-81c2-6bfa2f110288) [^41]

[^41]: E_Item_Category class diagram

![imagen](https://github.com/user-attachments/assets/8f6881f2-e6a6-447e-9eb7-2b7c87394908) [^42]

[^42]: E_Item_Category Values in Unreal Engine 5

```
enum E_Item_Category {
  Food,
  Material,
  Equipment,
  Potion,
  Junk
}
```
</details>

<details>

<summary>F_Slot_Struct</summary>

# F_Item_Struct

## What's can we change in the item slot?

This is a structure that defines what data is being shown for an item slot to be altered and changed in the code

### Structure Elements

![imagen](https://github.com/user-attachments/assets/18bf0fbf-6a22-44da-afbf-8fc23b4907b3) [^43]

[^43]: F_Slot_Struct class diagram

![imagen](https://github.com/user-attachments/assets/4cde0281-3092-4e31-929e-18314f552df9) [^44]

[^44]: F_Slot_Struct attributes in Unreal Engine 5

```
struct F_Slot_Struct {
  name itemID,
  int quantity
}
```
</details>

<details>

<summary>I_Interact_Interface</summary>

# I_Interact_Interface

## How can we interact

This is an interface with the sole purpose of giving certain functions that allows players to interact with certain objects.

### interface 

![imagen](https://github.com/user-attachments/assets/4d552861-cad2-4aec-b845-8016ccd89038) [^45]

[^45]: I_Interact_Interface class diagram

![imagen](https://github.com/user-attachments/assets/23199bf2-8c3a-43d8-80ac-6d57c36672c9) [^46]

[^46]: I_Interact_Interface in Unreal Engine 5

```
public class interface E_Item_Category {

  public lookAt();

  public interactWith();
}
```
</details>

<details>

<summary>DD_Inventory_Slot</summary>

# DD_Inventory_Slot

## Drag and Drop items

This is an Drag and Drop blueprint structure that allows the player to drag and drop items from their inventories

### Class structure

![imagen](https://github.com/user-attachments/assets/2527deb8-f745-4c3d-9508-e507073bd3c0) [^47]

[^47]: DD_Inventory_Slot class diagram

```
#import Inventory_System

public class DD_Inventory_Slot{

  Inventory_System invenotry;
  int contentIndex;

....
}
```

<summary>Event Graph</summary>

<ins>Event_DragCancelled</ins>

This event happens when the player stops the dragging of the mouse, it call the remove from inventory.

![imagen](https://github.com/user-attachments/assets/0613ea6e-10af-484c-946b-034cde26c89d) [^48]

[^48]: Event_DragCancelled event in Unreal Engine 5.

```
public event Event_DragCancelled(){
    RemoveFromInventory(inventory, contentIndex, true, false);
}
```

</details>

<details>

<summary>Item_Data</summary>

# Item_Data

## Every single item

This is a Data Table with the F_Data_Structure Data base, this allows us to add different types of items into the project.

### Current Data.

![imagen](https://github.com/user-attachments/assets/ca572b55-6800-4e6e-959e-96091e2e8bff) [^49]

[^49]: Item_Data Data Table diagram

![imagen](https://github.com/user-attachments/assets/85d57a2e-e094-4496-a785-5a2f7db3d25a) [^50]

[^50]: Item_Data configuration in Unreal Engine 5

</details>

<details>

<summary>Item_Data_Component</summary>

# Item_Data_Component

## How we add the items to an ivnentory

This is a component blueprint that is attached to items, so anyone can add them to their inventory. It has all the data from the specific item that is being added.

### Class structure

![imagen](https://github.com/user-attachments/assets/61c242af-ee49-4672-a46c-b1b5e55c1a3c) [^51]

[^51]: Item_Data_Component class diagram

```
#import Item_Data
#import Inventory_System


public class Item_Data_Component implements I_Interact_Interface{

  Data_Table_Row itemID;
  int quantity

....
}
```

<summary>Event Graph</summary>

<ins>Event_InteractWith</ins>

This event happens when a player interacts with the item.

![imagen](https://github.com/user-attachments/assets/89d7ca00-3b1a-4a44-ad84-088109f0d39e) [^52]

[^52]: Event_InteractWith event in Unreal Engine 5.

```
public event Event_InteractWith(BPThirdPersonCharacter playerCharacter){
    if(isValid(playerCharacter.inventorySystem)){
      if(AddToInventory(playerCharacter.inventorySystem, itemID.rowName, quantity).success){
        destroyActor(getOwner());
      }
    }
}
```

</details>

<details>

<summary>Chest</summary>

# Chest

## A simple container with an inventory

An actor that functions as a chest with

### Class structure

![imagen](https://github.com/user-attachments/assets/61c242af-ee49-4672-a46c-b1b5e55c1a3c) [^51]

[^51]: Item_Data_Component class diagram

```
#import Item_Data
#import Inventory_System

public class Item_Data_Component{

  Data_Table_Row itemID;
  int quantity

....
}
```

<summary>Event Graph</summary>

<ins>Event_InteractWith</ins>

This event happens when a player interacts with the item.

![imagen](https://github.com/user-attachments/assets/89d7ca00-3b1a-4a44-ad84-088109f0d39e) [^52]

[^52]: Event_InteractWith event in Unreal Engine 5.

```
public event Event_InteractWith(BPThirdPersonCharacter playerCharacter){
    if(isValid(playerCharacter.inventorySystem)){
      if(AddToInventory(playerCharacter.inventorySystem, itemID.rowName, quantity).success){
        destroyActor(getOwner());
      }
    }
}
```

</details>
