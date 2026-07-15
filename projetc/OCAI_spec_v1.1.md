# Definitions from the OCAI Language Specification. (OCAI - Object-oriented Context to AI)
Version: 1.1
Author: Luis Fernando Martinelli Ramos de Oliveira
Company: FP8 Studio

OCAI is based on Context-Oriented Programming rather than Memory-Oriented Programming. Objects do not expose passive state. Every interaction with an object is an explicit runtime operation. Therefore, all object interactions are expressed as methods and follow the Command–Query Separation principle. Query methods retrieve contextual information without modifying the object, while Command methods intentionally modify the contextual state.

This is a hybrid of a DSL based on programming syntax and contextual description.
The goal is to optimize the language, improve comprehension, avoid confusion in interpretation, and improve the organization of steps.
Objects do not expose passive fields. Every interaction with an object represents an action performed by the runtime. Therefore, OCAI exposes behavior through methods rather than properties.

# CORE PRINCIPLE
  OCAI models contextual knowledge rather than memory.
  Objects represent concepts.
  Methods represent interactions with those concepts.
  Every interaction is executed by the runtime.
  Therefore, methods are the fundamental abstraction of the language.
---

# LANGUAGE LOCALIZATION

The Runtime automatically presents all user-visible messages in the
user's language.

This behavior applies to:

- PRINT()
- ERROR()
- WARNING()
- INFO()
- Any other frontend-visible output.
---

# MARKDOWN SYNTAX
  To interpret instructions, ignore Markdown design syntax symbol sequences.
  Read and manipulate words without Markdown design.
---

# CASE-INSENSITIVITY RULE
  All textual comparisons are case-insensitive. This applies uniformly to:
  - Content and values assigned to variables.
  - Commands, clauses, and connectors (IF, AND, OR, NOT, FOR EACH, etc.).
  - Object identifiers, method names, and field names.
  No distinction is made between the interpretation of these categories — 
  the rule is global and requires no per-category exceptions.
---

# ARCHITECTURE
OCAI is composed of three conceptual layers.
1. **Language**:
Defines syntax and semantics.
2. **Runtime**:
Executes the language and exposes runtime services.
3. **Project Rules**:
Implement domain-specific logic using only the Language and Runtime.
---

# END OF PROCESS
 **$END**
 Stop rules and all process.
---

# GROUPING CONTEXTS AND VALUES

 ## SECTION
  **START**:
  A section is identified by a line containing only a title in uppercase 
  letters with Markdown title prefix "#", without any punctuation.
  **END**:
  Recognized by the character sequence "---".
  **SUMMARY**:
  A section starts with "#" + uppercase title and ends at the first "---" 
  sequence.
  **INTERRUPTION COMMAND**:
  *$STOP*: Immediately terminates evaluation of the current SECTION. 
  Ignores all remaining instructions, MODULEs, and SUBMODULEs inside 
  this SECTION. Continues evaluation from the next SECTION.

  ---

 ## MODULE
  **START**:
  A module is identified by a line containing only a title in uppercase 
  letters with Markdown title prefix "##", without any punctuation.
  **END**:
  Recognized by the character sequence "---". If a MODULE is nested 
  inside a SECTION, "---" closes both the MODULE and, if it is the last 
  MODULE in that SECTION, the SECTION as well — MODULE boundaries never 
  span across a "---" sequence.
  **SUMMARY**:
  A module starts with "##" + uppercase title and ends at the first 
  "---" sequence.
  **INTERRUPTION COMMAND**:
  *$EXIT*: Immediately terminates evaluation of the current MODULE. 
  Ignores all remaining instructions and SUBMODULEs inside this MODULE. 
  Continues evaluation from the next MODULE.

  ---

 ## SUBMODULE
   **START**:
   A submodule is identified by a line starting with "###" Markdown 
   prefix + uppercase title, without any punctuation.
   **END**:
   Recognized by the first empty line.
   **SUMMARY**:
   A submodule starts with "###" + uppercase title and ends at the 
   first empty line.
   **INTERRUPTION COMMAND**:
   *$NEXT*: Immediately terminates evaluation of the current SUBMODULE. 
   Ignores all remaining instructions. Continues evaluation from the 
   next SUBMODULE.

   ---

 ## BLOCK
   **START**:
   - A BLOCK is an atomic context container.
   - Identified by the pattern: reference name (variable, macro, 
     pipeline, dataset, loop statement, or condition clause) + "{" 
     character.
   - Can be used in method or property parameters without reference to 
     a variable.
   - The BLOCK extends the evaluation scope of the preceding statement.

   **END**:
   Recognized by the "}" character.

   **SUMMARY**:
   A block starts with reference name + "{" and ends at the first "}" 
   character.

   **SUPPORTED CONTEXTS**
   - variables
   - macros
   - pipelines
   - methods
   - clauses
   - datasets

   **Example 01**:
     IF: There is a character name
     AND if there is a city name.
     The condition only evaluates the IF line, and thus only considers 
     whether the character name exists. The 2nd line is an independent 
     instruction.

   **Example 02**:
     IF: { There is a character name
     AND if there is a city name. }
     Here, the 2 lines are grouped into a single scope and are both 
     part of the IF's condition.

   **Example 03 — @variable block**:
      @speaking = {
         "- Hi, my name is Jack and I'm just a test in this language rules.
         - Do you want to test it with me?"
      }
      All characters between "{}" are assigned as a single string value 
      to @speaking — the block is not split into items, since @variable 
      stores one value only.

   **Example 04**:
     @speaking.REMOVE({"- Hi, my name is Jack and"})
     All characters between "{}" are processed by the REMOVE method as 
     a single literal value.

   **Example 05**:
     &character.biography = {"Jack is an adult man, and he is helping 
     this language project."}
     All characters between "{}" are assigned to the "biography" field 
     of the &character dataset.

   **Example 06 — $Pipeline[] block**:
     See $Pipeline[] → DIRECT BLOCK ASSIGNMENT for the multi-instruction 
     splitting behavior specific to Pipelines.

---

# GLOSSARY
Variable = Stores a single value
Dataset  = Stores structured data
Macro    = Stores executable logic
List     = Variable list
Pipeline = Macro list
Workflow = Pipeline collection, addressed by context
Runtime  = Executes the language
Asset    = File supplied by the frontend
Query    = Search expression accepted by runtime methods
---

# DATA TYPES

1. **String**
   Sequence of characters between quotes "".

   *STORAGE*
   - Static and literal character sequence.
   - Not a context and does not trigger processing.

   *PATTERN*
   Identified by quote + character sequence + quote, i.e. a sequence 
   starting and ending with quote "".

   *USED IN*
   - As literal text inside a context text.
   - `@variable` object.
   - `list[]` object.
   - Method parameter, within a parameter pattern `()`.

   *EXAMPLE*
   @variable = "use this text"
   [object].[method]("use this text")

2. **Number**
   Numeric value.

   *STORAGE*
   Number, index, or value.

   *PATTERN*
   Identified by a number without quotes, i.e. not enclosed in quotes.

   *USED IN*
   - As literal value inside a context text.
   - `@variable` object.
   - `list[]` object.
   - Method parameter, within a parameter pattern `()`.
   - Index parameter in list-type objects.
   - Math operations.

   *EXAMPLE*
     @age = 18
     mylist[1]

3. **JSON**
    Structured data container.

    *STORAGE*
    Data in JSON format, using standard `"key": value` syntax.

    *PATTERN*
    Identified by JSON format.

   *EXAMPLE*
   &dataset[].ADD({"field1": "value1", "field2": "value2"})

4. **Context Text**
   A block of text containing process instructions used to execute a 
   task within a given context.

   *STORAGE*
   - A static, literal character sequence.
   - Represents executable contextual intent. It is interpreted only when consumed by a runtime operation.

   *PATTERN*
   Identified by a character sequence — i.e., a 
   sequence that not both starts and ends with a double quote `""`.

   *USED IN*
   - Inside a context text, to represent literal text.
   - `$Macro` object.
   - `$Pipeline[]` object.
   - `$Workflow()` object.
   - Method parameter, within a parameter pattern `()`.

   *EXAMPLE*
   $macro = use this text
   [object].[method](use this text)

5. **Tag**
   Sequence of characters starting with "#".

   *STORAGE*
   - Identifier: static and literal character sequence.
   - Not a context and does not trigger processing.
   - Not a String.
   - Not a Number.

   *PATTERN*
   Identified by "#" + character sequence, i.e. a sequence starting 
   with "#".

   *EXAMPLE*
   If the user provides #clothing, then get the clothes in the image.

6. **Bool**
   Bolean value.

   *STORAGE*
   true or false

   *PATTERN*
   Identified by the litteral words true or false without quotes, i.e. not enclosed in quotes.

   *USED IN*
   - As literal value inside a context text.
   - `@variable` object.
   - `list[]` object.
   - Method parameter, within a parameter pattern `()`.
   
   *EXAMPLE*
     @age = true
     mylist[1]

---

# SUPPORTED OPERATORS
  +:
    - Used only with numbers; performs math operations.
    - Can operate on values inside variables, list items, and dataset 
      registers.
    - The value must be a number. If it is not, the math operation is 
      not processed and no error is raised.

   **Example**
     `Variable`:
      @age = 20 -> @myvalue = @age + 1 -> @myvalue is 21
     `List item`:
      mylist[].ADD(20) -> @myvalue = mylist[1] + 1 -> @myvalue is 21
     `Dataset`:
      &mydata.age = 20 -> @myvalue = &mydata.age + 1 -> @myvalue is 21

  +=:
    - Same as "+", but only used with variables, list items, or dataset 
      registers.
    - Processes the operation and assigns the result to the same 
      variable, list item, or dataset register.

   **Example**
     `Variable`:
      @age = 20 -> @age += 1 -> @age is 21
     `List item`:
      mylist[].ADD(20) -> mylist[1] += 1 -> mylist[1] is 21
     `Dataset`:
      &mydata.age = 20 -> &mydata.age += 1 -> &mydata.age is 21

  -:  Same rules and process as "+", for subtraction.
  -=: Same rules and process as "+=", for subtraction.
  *:  Same rules and process as "+", for multiplication.
  *=: Same rules and process as "+=", for multiplication.
  /:  Same rules and process as "+", for division.
  /=: Same rules and process as "+=", for division.

  ||:
    - Used to concatenate strings.
    - Can concatenate strings inside variables, list items, and dataset 
      registers.

   **Example**
     `Variable`:
      @name = "" -> @myvalue = "John " || "Doe" -> @myvalue is "John Doe"
     `List item`:
      mylist[].ADD("John ") -> @myvalue = mylist[1] || "Doe" -> @myvalue is "John Doe"
     `Dataset`:
      &mydata.name = "John " -> @myvalue = &mydata.name || "Doe" -> @myvalue is "John Doe"

  ||=:
    - Same as "||", but only used with variables, list items, or 
      dataset registers.
    - Processes the operation and assigns the result to the same 
      variable, list item, or dataset register.

   **Example**
     `Variable`:
      @name = "John " -> @name ||= "Doe" -> @name is "John Doe"
     `List item`:
      mylist[].ADD("John ") -> mylist[1] ||= "Doe" -> mylist[1] is "John Doe"
     `Dataset`:
      &mydata.name = "John " -> &mydata.name ||= "Doe" -> &mydata.name is "John Doe"

---

# FRONTEND OBJECT
  Runtime interface to the frontend environment.

  ## SYNTAX
   @FRONTEND + dot + field or method.

   **Example**:
   @FRONTEND.PRINT("Hello world!") -> Sends "Hello World" message to user.
   @FRONTEND.CHAT() -> Returns what the user wrote in the chat command box.

  ## QUERY METHODS

   ### CHAT
   Runtime Method
   Returns the current chat content.

   **SYNTAX**
    @FRONTEND.CHAT

   **PARAMETERS**
    Does not accept parameters.

   **EXAMPLE**:
      User wrote in chat box: "Make a summary of my text" 
      -> @FRONTEND.CHAT returns "Make a summary of my text".

   **ON ERROR**:
      Attempting to set any content or value -> Raise 
      $ERROR("Can't send message to chat box!").

   ### CHAT.TAGS[index]
     A CHAT sub-method that returns only the tags written in the 
     user's chat instructions.

     **SYNTAX**
      @FRONTEND.CHAT.TAGS[index]

     **RETURNS**
      If index is omitted, returns all tags from the chat instructions.
      If index is provided, returns the tag at that position.
      Example: "#clothing, #shoes, #body, generate scene with the 
      character." -> @FRONTEND.CHAT.TAGS[2] -> Returns #shoes

     **INDEX**
     Index -> Numeric position of the tag within the tag sequence.
     Example: @FRONTEND.CHAT.TAGS[1] -> First tag.

     **EXAMPLE**:
     User chat = "Wearing character with the #shirt, #shoes, #pants"
     @FRONTEND.CHAT.TAGS[] -> #shirt, #shoes, #pants
     @FRONTEND.CHAT.TAGS[2] -> #shoes

   ### CHAT.TEXT()
   A CHAT filter sub-method that returns only the text, with tags removed, 
   from the user's chat instructions.

   **SYNTAX**
    @FRONTEND.CHAT.TEXT(query)

   **RETURNS**
    Ignores tags and returns only the text portion of the chat 
    instructions.
    Example: "#clothing, #shoes, #body, generate scene with the 
    character." -> @FRONTEND.CHAT.TEXT() -> Returns "generate scene 
    with the character."
    If there is no text content, returns an empty string "".

   **PARAMETERS**
    No accepts parameters

   **EXAMPLE**:
     User chat = "Wearing character with the #shirt, #shoes, #pants"
     @FRONTEND.CHAT.TEXT() -> "Wearing character with the"

   ### ASSETS[index]
   Accesses chat asset files.

   **RETURNS**
   - Returns a single asset file.
   - Returns multiple files as a &dataset[]. Each asset becomes a new 
     item in the &dataset[].

   **SYNTAX**
     @FRONTEND.ASSETS[index]

   **INDEX**
   - Number: position of the file within Assets.
   - Omitted: all assets, returned as a &dataset[].

   **ON ERROR**:
   - Attempting to set any content or value -> Raise $ERROR("Can't edit 
     data of assets!").
   - A context text request incompatible with the file type (e.g. 
     asking for image information on a text asset) -> Raise 
     $ERROR("Asset type mismatch! The asset can't accept solicitation!").

   **EXAMPLE**:
     &character[].EMBED(@FRONTEND.ASSETS[1], "photo_type, local, lighting")
     -> Adds a JSON item describing asset 1.

  ---

  ## COMMAND METHODS

   ### PRINT(parameter)
    Runtime Method
    Sends a message to the frontend.
    The Runtime automatically displays the message in the user's language.

   **SYNTAX**
     @FRONTEND.PRINT(parameter)

   **PARAMETERS**
     String -> Message to the user.

   **ON ERROR**:
    - Receiving any non-string content or value -> ignore, no error 
      raised.
    - Sending text without enclosing quotes -> Raise $ERROR("Message 
      type mismatch! Only send a string message.").

   **EXAMPLE**:
     @FRONTEND.PRINT("Message type mismatch! Only send a string message.")

---

# $ERROR(message) METHOD
  Interrupts pipelines and tasks, then executes @FRONTEND.PRINT(message).
  The Runtime automatically displays the message in the user's language.
---

# OBJECTS IDENTIFIERS
Language identifiers define the storage object used by the runtime.
Each identifier has its own storage model, supported operations, and 
return behavior.

 ## @Variable:
  A value container for temporary value storage.

  ### SYNTAX
   "@" symbol + sequence of characters without spaces and without 
   punctuation.
   **Example**: @name

  ### STORAGE
   One string, number value or bool value.

  ### RETURNS
   Returns the same string, number or bool value stored.
   **Example**:
      @character_genre = "female" -> Returns: female
      @character_age = 21 -> Returns: 21
      @ischaracter = true -> Returns true

---

 ## $Macro
  Stores exactly one executable contextual instruction represented as
  Context Text.
  A Macro encapsulates the execution of a single task.
  Macros are never executed automatically; they execute only when
  explicitly called through EXECUTE(), either directly or as part of a
  $Pipeline[].
  When a String value is assigned to a $Macro, the Runtime automatically casts the String to Context Text by removing the surrounding quotes.

   ### PURPOSE
   A $Macro represents the smallest executable unit in the language.
   Its primary role is to be stored as an item of a $Pipeline[].
   When a Pipeline is executed, each stored Macro is executed
   sequentially in insertion order.
   A $Macro may also be executed independently, without belonging to any
   Pipeline.

   ### SYNTAX
   "$" symbol + sequence of characters without spaces or punctuation.
   **Example**
   $generate_text

   ### STORAGE
   Stores exactly one executable Context Text.
   Strings assigned to a $Macro are automatically converted to Context Text before storage.

   ### TYPE CAST
   Whenever a $Macro is required by the Runtime and a Context Text is
   provided instead, the Runtime automatically creates an anonymous
   temporary $Macro containing that Context Text.
   The generated Macro behaves exactly like an explicitly declared
   $Macro, but exists only for the duration of the current operation.
   This conversion is transparent to the project rules.
   **Example**
   $pipeline[].ADD(
      Generate a professional studio lighting setup.
   )
   Equivalent to:
   $temp = {
      Generate a professional studio lighting setup.
   } 
   $pipeline[].ADD($temp)

   ### RETURNS
   Without EXECUTE():
   Returns the stored Context Text.
   With EXECUTE():
   Returns the result produced by executing the stored instruction.

   ### PARAMETERS
   Does not accept parameters.

   ### EXAMPLES
   $generate_prompt = {
      Generate a prompt describing a professional fashion photograph.
   }
   $string_prompt = "White light" -> The Runtime stores: White light
   @prompt = $generate_prompt.EXECUTE()
   $pipeline[].ADD(
      Preserve the character identity exactly as provided.
   ) -> The Runtime automatically converts the Context Text into a temporary $Macro before adding it to the Pipeline.

---

# LISTS ARCHITECTURE
  All list objects must respect:
  - Container ordered by items (lines) identified by index number.
  - Each item is independent data, a query, or a value.
  - The object name ends with a "[]" index pattern to select the item 
    to get, set, or execute.
  - Empty "[]" -> selects all items.
  - To assign new content, create a new item using the ADD() method.
  - To assign content to an existing item, provide its index in "[]" 
    pattern.
  - To remove/delete an item, provide its index and use REMOVE().
  - To remove all items, use empty "[]" with REMOVE().
  - To remove or edit items matching a query occurrence, use empty "[]" 
    with APPLY(query).

# OBJECTS COLLECTION TYPES

  ## DIRECT COLLECTION ASSIGNMENT
  When assigning a block ({}) to any Collection object, the Runtime interprets each top-level item as one collection element.

  The Runtime automatically instantiates the appropriate element type according to the target collection.
  The syntax used to delimit top-level items is defined by the language grammar (numbered items, "-", etc.).

  Direct Collection Assignment is declarative syntax. 
  The Runtime expands it into the equivalent sequence of primitive collection operations before execution.

  List<T> → T
  Dataset → JSON record
  Pipeline → Macro
  ...

  ## List<T>[index]
   An ordered generic collection whose items are accessed by index.

   ### STORAGE
     Stores values compatible with its declared type.
     Each list item is an independent object.
     Supported value types include primitive values, assets, objects, 
     datasets, and other DSL-supported types.

   ### ASSIGNMENT
     Single item:
     list[].ADD(value)
     Whole list:
     new[] = old[]
     Whole-list assignment copies the collection contents.

   ### RETURN
     list[index] -> Returns the item at the specified index.
     list[] -> Returns all items in the collection.

   ### EXAMPLES
     mylist[].ADD("Fernando")
     mylist[].ADD(@FRONTEND.ASSETS[1])
     mylist[] = @FRONTEND.ASSETS.GET("image files")

---

  ## &Dataset[]:
   Stores a list of JSON data. Each item is a JSON object.
   Dataset is a specialization of List<JSON>.
   Items can be provided in two ways: as ready-made JSON via ADD(), or 
   generated from an asset via EMBED().

   ### SYNTAX
     Prefix symbol is "&" and ends with index pattern "[]".
     **Example**: &character[]

   ### STORAGE
     - Each item is an independent JSON object.
     - Content can only be assigned through the ADD() or EMBED() 
       methods.
     - Stores the JSON result of any Query or Macro execution.
     - If a Query or Macro result is not JSON-compatible, the item is 
       discarded and the &Dataset remains unchanged. No error is 
       raised.

   ### ADD() BEHAVIOR
     - ADD(JSON) assigns a ready-made JSON object directly as a new 
       item.
     - The JSON content and its validation follow the general JSON 
       rules already defined in Data Types; ADD() does not alter or 
       generate any fields on its own.

   ### EMBED() BEHAVIOR
     - EMBED(asset, jsonFields) generates a new JSON item from an 
       asset, rather than receiving one directly.
     - `asset` — the source asset to extract data from.
     - `jsonFields` — the set of fields to extract from the asset and 
       include in the generated JSON.
     - In addition to the extracted fields, EMBED() automatically adds:
       - `"id"` — an identifier generated from the asset's 
         identification.
       - `"index"` — the numeric position recording the order in which 
         the asset was embedded into the Dataset.
     - The `"id"` and `"index"` fields are assigned automatically and 
       are not user-editable.

   ### RETURNS
     - The full JSON data of an item, when accessed without a field 
       path.
     - A specific field value, when accessed via `[index].field`.
     - Any JSON result registered through the EXECUTE(query) method 
       parameter.

   ### PARAMETERS
     - `[index]` — selects and returns a single item from the list, by 
       its position, following the standard index pattern already 
       defined for list-type objects.

   ### EXAMPLES
     &character[].EMBED(@FRONTEND.ASSETS[1], "character details")
     → Extracts the indicated fields from the asset and stores them as 
     a structured JSON object, e.g.:
     `{"id": "asset_1", "index": 1, "character details": "..."}`

     @eyes_color = &character[1].eyes_color
     → Retrieves the value of the "eyes_color" field from item 1 of 
     the &character Dataset.

---

  ## $Pipeline[]:
   Stores an ordered collection of $Macro items, each representing a 
   single executable contextual instruction.
   A Pipeline does not hold instructions directly — it holds 
   references to Macros, which in turn hold the instructions.
   Pipelines are never executed automatically; they execute only when 
   explicitly called through EXECUTE().

   ### PURPOSE
     A $Pipeline[]'s role is to group multiple $Macro items into a 
     single executable sequence. Once one or more Macros are added, 
     executing the Pipeline triggers the execution of every Macro it 
     contains, in the order they were added.
     A Pipeline with no Macros added is valid but executes nothing.

   ### SYNTAX
     "$" symbol + sequence of characters without spaces and without 
     punctuation + "[]" suffix, marking it as a list-type object.
     **Example**: $render_sequence[]

   ### STORAGE
     Stores an ordered list of Macro references.

   ### DIRECT BLOCK ASSIGNMENT
     A $Pipeline[] may be assigned directly using a "{}" block 
     containing one or more instruction lines, following the general 
     BLOCK pattern defined in GROUPING CONTEXTS AND VALUES.

     **RULE**: Each top-level item inside the block is treated as one independent instruction.
     The runtime automatically creates one $Macro one Macro per top-level item and appends it to the 
     Pipeline via ADD(), preserving line order as index order. Lines 
     are split by their instructional boundary, one Macro per top-level item describing 
     a distinct task becomes its own Macro, even if the lines share 
     the same subject.

     This is functionally equivalent to calling ADD() once per line, 
     with the runtime generating one implicit $Macro per line.

     **Example**:
     ```
     $pipeline[] = {
        1. Dress &character[1] with &clothing[1].
        2. Dress &character[1] with $shoes[1].
     }
     ```
     This block contains two distinct instructions — dressing the 
     character in clothing, and putting on shoes — and is therefore 
     split into two separate Macro items:
     - Item 1: "Dress &character[1] with &clothing[1]."
     - Item 2: "Dress &character[1] with $shoes[1]."

     This is equivalent to:
     ```
     $pipeline[].ADD("Dress &character[1] with &clothing[1].")
     $pipeline[].ADD("Dress &character[1] with $shoes[1].")
     ```

   ### RETURNS
     When EXECUTE() is not called, returns the ordered list of Macro 
     references currently stored (unexecuted).
     When EXECUTE() is called, returns the combined result of 
     executing every Macro in the Pipeline, in sequence.

   ### PARAMETERS
     Does not accept parameters on its own. Methods called on it 
     (ADD, REMOVE, EXECUTE) follow the standard Parameter delimiter 
     pattern "()".

   ### EXAMPLES
   $character = "Search for a character image"
   $background = "Generate a background scene"

   $render_sequence[].ADD($character)
   $render_sequence[].ADD($background)
   → Adds `$character` and `$background`, in that order, to the 
   Pipeline `$render_sequence[]`.

   `@result = $render_sequence[].EXECUTE()` → Executes `$character` 
   then `$background`, in sequence, and assigns the combined result 
   to `@result`.

   `$render_sequence[].REMOVE($background)` → Removes `$background` 
   from the Pipeline; a subsequent EXECUTE() would run only 
   `$character`.

---

  ## $Workflow():
 Stores an ordered collection of $Pipeline[] items, each representing a 
 group of executable Macro instructions.
 Pipelines can be retrieved by semantic context or by their identifier.
  Unlike $Pipeline[], which controls its items by numeric index "[]", 
 $Workflow() controls its items by context — each stored Pipeline is 
 identified and retrieved through a context-matching instruction 
 passed as a parameter, following the Parameter delimiter pattern "()".
 Execution of the stored instructions only occurs when the EXECUTE() 
 method is called, respecting the addition order recorded in the 
 "index" field of each entry's internal JSON.

 NOTE: "$Workflow()" denotes the object's *type*, not a fixed name. 
 The developer may assign any valid identifier to it, provided the 
 syntax rules below are respected.

 ### PURPOSE
   A $Workflow()'s role is to group multiple $Pipeline[] items into a 
   single addressable collection, where each stored Pipeline can be 
   identified and executed individually through context — not through 
   position. This allows instructions to reference a Pipeline by what 
   it represents ("dress character"), rather than by where it sits in 
   a list.

 ### SYNTAX
   "$" symbol + sequence of characters without spaces and without 
   punctuation + "()" suffix, marking it as a context-controlled object 
   (as opposed to the "[]" suffix used for index-controlled lists).
   **Example**: $production_flow()

 ### STORAGE
   An ordered collection of Pipeline entries, each stored internally as 
   a JSON object with three keys:
   - `"id"` — the identifier taken from the name of the $Pipeline[] 
     variable that was added (e.g. `$dress_character[]` → 
     `"dress_character"`).
   - `"context"` — the character sequence of the instructions 
     associated with that Pipeline, used as the basis for context 
     matching.
   - `"index"` — the numeric position recording the order in which the 
     Pipeline was added to the Workflow. This value determines 
     execution order and is assigned automatically; it is not 
     user-editable.

   **Example of a stored entry**:
   `{"id": "dress_character", "context": "instructions...", "index": 1}`

 ### RETURNS
   When EXECUTE() is not called, returns the stored JSON entry (or 
   entries) matching the given context parameter — or the full JSON 
   collection, ordered by "index", if no context parameter is provided.
   When EXECUTE() is called, returns the result of executing the 
   instructions of the Pipeline(s) matched by context, processed in 
   ascending "index" order.

 ### PARAMETERS
   Accepts a context-identification instruction within the "()" 
   pattern, used to select which stored Pipeline(s) to reference. 
   Matching is performed against the stored `"id"` and/or `"context"` 
   fields: 
   1. id
   2. context
   Not against "index" or any numeric position.

   **LITERAL STRING RULE**: When identifying a Pipeline by name inside 
   the "()" parameter, the name must always be provided as a literal 
   string enclosed in quotes `""`, following the Context Text pattern 
   already defined in Data Types. This removes any ambiguity between a 
   literal identifier and a dynamic expression.
   - Correct: `$production_flow("dress character")`
   - Incorrect: `$production_flow(dress character)`

   EXECUTE() may additionally accept an inline instruction as a 
   parameter, also provided as a literal string, used to extend or 
   refine the context passed to the matched Pipeline's execution.

 ### EXECUTION RULE
   Adding a Pipeline to a $Workflow() only stores its reference; it 
   does not trigger execution. Instructions stored within a 
   $Workflow() are only executed when EXECUTE() is explicitly called. 
   If EXECUTE() is called without a context parameter, all stored 
   Pipelines are executed in ascending order of their "index" field. If 
   a context parameter is provided, only the Pipeline(s) matching that 
   context are executed.

 ### EXAMPLES
   $dress_character[].ADD($change_outfit)
   $dress_character[].ADD($adjust_pose)
   $production_flow().ADD($dress_character[])
   → Stores: `{"id": "dress_character", "context": "instructions...", "index": 1}`
   `$production_flow("dress character").EXECUTE("generate prompt text")`
   → Identifies the stored Pipeline whose context matches "dress 
   character", then executes it with the additional instruction 
   "generate prompt text" applied to the execution context.

 ...
---

# OBJECTS METHODS ACCESS
  Always after a dot in the name of the Identifier object, ending with 
  parameter pattern "()".
  *Direct*: object.method(), object[].method()
  *Assigning Parameter*: object.method(string, number, context text, 
  asset), object[].method(string, number, context text, asset)

---

# OBJECT × METHOD SUPPORT MATRIX
  The following table is the single source of truth for which methods 
  are supported by which objects. Any method section below must match 
  this table; the table takes precedence in case of conflict.

  | Method    | @variable | list[] | &dataset[] | $Macro | $Pipeline[] | $Workflow() |
  |-----------|:---------:|:------:|:----------:|:------:|:-----------:|:-----------:|
  | ADD       |     -     |   ✔    |     ✔      |   -    |      ✔      |      ✔      |
  | EMBED     |     -     |   -    |     ✔      |   -    |      -      |      -      |
  | REMOVE    |     -     |   ✔    |     ✔      |   -    |      ✔      |      ✔      |
  | CLEAR     |     ✔     |   ✔    |     ✔      |   -    |      ✔      |      ✔      |
  | EXECUTE   |     -     |   -    |     -      |   ✔    |      ✔      |      ✔      |
  | SET       |     ✔     |   ✔    |     ✔      |   ✔    |      ✔      |      -      |
  | GET       |     ✔     |   ✔    |     ✔      |   ✔    |      ✔      |      -      |
  | COUNT     |     -     |   ✔    |     ✔      |   -    |      ✔      |      ✔      |
  | EMPTY     |     ✔     |   ✔    |     ✔      |   -    |      ✔      |      ✔      |
  | INDEXOF   |     -     |   ✔    |     ✔      |   -    |      ✔      |      -      |
  | EXISTS    |     ✔     |   ✔    |     ✔      |   -    |      ✔      |      -      |

  NOTE: $Workflow() is not index-based, so INDEXOF() is not supported 
  — matching is done by EXECUTE()/GET() with a literal string context 
  parameter instead. SET()/GET() are not supported on $Workflow() 
  because its content is only ever modified through ADD()/REMOVE() of 
  whole Pipeline entries, never through partial in-place editing.

---

# QUERY METHODS

  ## COUNT(parameter)
   Returns the number of items in a list.

   ### SYNTAX
    mylist[].COUNT()
    mylist[].COUNT(parameter)

   ### PARAMETER
    - None -> Returns the total number of items.
    - Query -> Returns the number of items matching the query.

   ### SUPPORTED OBJECTS
     list[], &dataset[], $pipeline[], $workflow()

   ### EXAMPLES
      mylist[].COUNT() -> Returns all items.
      mylist[].ADD("test"), mylist[].ADD("again") -> mylist[].COUNT() is 2.
      mylist[].ADD("this is a test"), mylist[].ADD("again"), 
      mylist[].ADD("More test") -> mylist[].COUNT("item with a 'test' word") is 2.

  ## EMPTY()
   Checks whether the object contains any stored content.
   For collection objects, EMPTY() is equivalent to COUNT() == 0.
   For single-value objects, EMPTY() returns true when no value is stored.

   ### SYNTAX
     myList[].EMPTY()
     &dataset[].EMPTY()
     @variable.EMPTY()
     $pipeline[].EMPTY()
     $workflow().EMPTY()

   ### PARAMETER
     Does not accept parameters.

   ### RETURN
    true : No content is stored in the object.
    false : At least one value is stored in the object.

   ### BEHAVIOR
   EMPTY() never modifies the object.
   The evaluation depends on the object type:
   - Variable → true if no value is assigned.
   - Collection → true if COUNT() == 0.
   - Workflow → true if no Pipeline is registered.

   ### SUPPORTED OBJECTS
     @variable, list[], &dataset[], $pipeline[], $workflow()

   ### EXAMPLES
      @variable = "test" -> @variable.EMPTY() is false -> 
      @variable.CLEAR() -> @variable.EMPTY() is true.
      mylist[].ADD("test"), mylist[].ADD("again") -> mylist[].EMPTY() is false.
      &dataset[].ADD({"name": "John Doe", "age": "18"}) -> 
      &dataset[].CLEAR() -> &dataset[].EMPTY() is true.
      $pipeline[].ADD($macro) -> $pipeline[].CLEAR() -> $pipeline[].EMPTY() is true.

  ---

  ## NOTEMPTY()
   Checks whether the object contains any stored content.
   For collection objects, NOTEMPTY() is COUNT() > 0.
   For single-value objects, NOTEMPTY() returns true when value is stored.

   ### SYNTAX
     myList[].NOTEMPTY()
     &dataset[].NOTEMPTY()
     @variable.NOTEMPTY()
     $pipeline[].NOTEMPTY()
     $workflow().NOTEMPTY()

   ### PARAMETER
     Does not accept parameters.

   ### RETURN
    false : No content is stored in the object.
    true : At least one value is stored in the object.

   ### BEHAVIOR
   NOTEMPTY() never modifies the object.
   The evaluation depends on the object type:
   - Variable → false if no value is assigned.
   - Collection → false if COUNT() == 0.
   - Workflow → false if no Pipeline is registered.

   ### SUPPORTED OBJECTS
     @variable, list[], &dataset[], $pipeline[], $workflow()

   ### EXAMPLES
      @variable = "test" -> @variable.NOTEMPTY() is true -> 
      @variable.CLEAR() -> @variable.NOTEMPTY() is false.
      mylist[].ADD("test"), mylist[].ADD("again") -> mylist[].NOTEMPTY() is true.
      &dataset[].ADD({"name": "John Doe", "age": "18"}) -> 
      &dataset[].CLEAR() -> &dataset[].NOTEMPTY() is false.
      $pipeline[].ADD($macro) -> $pipeline[].CLEAR() -> $pipeline[].NOTEMPTY() is false.

  ---

  ## INDEXOF(query)
  Returns the index of the first item that semantically matches the 
  query. Returns -1 if no item matches.

   ### QUERY
     Context instruction to search for a corresponding item. Returns 
     the index where the query instruction's content occurs.

   ### SYNTAX
     mylist[].INDEXOF(query)
     &dataset[].INDEXOF(query)

   ### SUPPORTED OBJECTS
     list[], &dataset[], $pipeline[], @FRONTEND.ASSETS[]

   ### EXAMPLES
     mylist[].INDEXOF("item with age word")
     &dataset[].INDEXOF("eyes_color is green")
     $pipeline[].INDEXOF("character instructions")

  ---

  ## EXISTS(query)
  Returns TRUE if at least one item semantically matches the specified 
  query. Otherwise returns FALSE.

   ### QUERY
     A semantic description used to identify one or more matching 
     items. The query is evaluated by meaning rather than exact text 
     matching.

   ### SYNTAX
     @variable.EXISTS(query)
     @FRONTEND.CHAT().TAGS[].EXISTS(query)
     @FRONTEND.CHAT().TEXT().EXISTS(query)
     @FRONTEND.ASSETS[].EXISTS(query)
     mylist[].EXISTS(query)
     &dataset[].EXISTS(query)

   ### SUPPORTED OBJECTS
     list[], &dataset[], $pipeline[], @FRONTEND.ASSETS[], @variable, 
     @FRONTEND.CHAT().TAGS[], @FRONTEND.CHAT().TEXT()

   ### EXAMPLES
     @variable.EXISTS("any comma")
     @FRONTEND.CHAT().TAGS[].EXISTS("a #eyes tag")
     @FRONTEND.CHAT().TEXT().EXISTS("about lighting instructions")
     @FRONTEND.ASSETS[].EXISTS("image file")

  ## LISTOF(field)
  Returns the values of the specified JSON field from every item in a
  &Dataset[].

   ### BEHAVIOR
   Iterates through the Dataset in ascending index order.
   For each item:
   - If the requested field exists, its value is appended to the result.
   - If the requested field does not exist, that item is ignored.
   - Processing continues for all remaining items.
   - No error is raised.
   The original Dataset is never modified.

   ### PARAMETER
   **field**
   Optional.
   Specifies the JSON field whose values should be returned.
   - If provided, returns the values of that field from every Dataset item.
   - If omitted, returns every complete JSON object stored in the Dataset.

   ### RETURN
   If **field** is provided:
   - Returns a comma-separated string containing the values of the
   specified field, preserving Dataset order.
   Example:
   "name1,name2,name3"
   If **field** is omitted:
   - Returns every JSON object in the Dataset separated by semicolons (`;`),
    preserving Dataset order.
   Example:
   {"id":"1","name":"John"};{"id":"2","name":"Mary"}
   If no matching values are found, returns `""`.

   ### SYNTAX
   &dataset[].LISTOF(field)

   ### SUPPORTED OBJECTS
   - &Dataset[]

   ### EXAMPLES
   Return one field from all items:
   &characters[].LISTOF("name") -> "John,Mary,Peter"
   Return another field:
   &characters[].LISTOF("eyes_color") -> "blue,brown,green"
   Return every JSON object:
   &characters[].LISTOF() -> {"id":"asset1","name":"John","eyes_color":"blue"}; {"id":"asset2","name":"Mary","eyes_color":"brown"}; {"id":"asset3","name":"Peter","eyes_color":"green"}
   
   Return values from a field that does not exist:
   &characters[].LISTOF("hair_style") -> ""

---

# COMMAND METHODS

  ## CLEAR()
  A void method. Clears the content of an object.

   ### SYNTAX
     [object].CLEAR()

   ### PARAMETER
     Does not accept parameters.

   ### SUPPORTED OBJECTS
     @variable, list[], &dataset[], $pipeline[], $workflow()

   ### EXAMPLE
     @variable.SET("test") -> @variable.GET() returns "test" -> 
     @variable.CLEAR() -> @variable.GET() returns "".

  ## EXECUTE(parameter)
  Executes the instructions stored in one or more context objects.
  The execution behavior depends on the target object type.

   ### SYNTAX
   [object].EXECUTE()
   [object].EXECUTE("context")

   ### PARAMETER
   Optional.
   When provided, the parameter must be a Context Text (literal string enclosed in quotes).
   The meaning of the parameter depends on the target object:
   • Objects that contain a single instruction
    The parameter extends the execution context.
   • Objects that contain multiple instructions
    The parameter selects which stored instructions will be executed.
   See **BEHAVIOR BY OBJECT** below.

   ### SUPPORTED OBJECTS
   $Macro
   $Pipeline[]
   $Workflow()

   ### BEHAVIOR BY OBJECT

   **$Macro**
   A Macro stores exactly one contextual instruction.
   - `$macro.EXECUTE()`
   Executes the stored instruction.
   - `$macro.EXECUTE("additional context")`
   Executes the stored instruction using the supplied context as additional execution information.
   The parameter never selects instructions because a Macro always contains exactly one instruction.
   
   **$Pipeline[]**
   A Pipeline stores an ordered list of Macro items.
   - `$pipeline[index].EXECUTE()`
   Executes only the Macro at the specified index.
   - `$pipeline[].EXECUTE()`
   Executes every Macro in ascending index order.
   - `$pipeline[].EXECUTE("query")`
   Executes only the Macro items whose stored instructions semantically match the supplied query.
   The parameter acts as a selection filter.

   **$Workflow()**
   A Workflow stores an ordered collection of Pipelines identified by context.
   - `$workflow("context").EXECUTE()`
   Executes only the Pipeline matching the supplied context.
   - `$workflow().EXECUTE()`
   Executes every stored Pipeline in ascending index order.
   - `$workflow().EXECUTE("query")`
   Executes only the Pipelines whose stored context semantically matches the supplied query.
    The parameter acts as a selection filter.

   ### EXAMPLES
     $macro = "Generate prompt text to adjust lighting"
     @result = $macro.EXECUTE("using sunset lighting")
     → Executes the Macro while extending its execution context with "using sunset lighting".
     $pipeline[] = {
        1. Dress &character[1] with &clothing[1].
        2. Dress &character[1] with $shoes[1].
     }
     $pipeline[].EXECUTE()
     → Executes both Macro items.
     $pipeline[2].EXECUTE()
     → Executes only Macro 2.
     $pipeline[].EXECUTE("instructions about clothing")
     → Executes only the Macro items matching that query.
     $workflow("dress character").EXECUTE()
     → Executes only the Pipeline identified by the supplied context.
     $workflow().EXECUTE("character generation")
     → Executes only the Pipelines matching that contextual query.
  ...

  ## SET(query)
     Modifies the content of the current object according to the 
     specified semantic query.

   ### SYNTAX
     [object].SET(query)

   ### PARAMETER
     Semantic query describing the modification to apply.

   ### SUPPORTED OBJECTS
     @variable, list[], $Macro, &dataset[], $Pipeline[]

   ### EXAMPLE
     @variable = "Just a new Test" -> @variable.SET("remove Test") -> 
     @variable is "Just a new".

     $pipeline[] = {
      - Dress &character[1] with &clothing[1].
      - Dress &character[1] with $shoes[1].
     } -> $pipeline[1].SET("replace &clothing[1] with &clothing[2]") -> 
     $pipeline[1] is "Dress &character[1] with &clothing[2]".

     &character[].ADD({"name": "John Doe", "age": "21", "eyes_color": "green"}) 
     -> &character[1].SET("replace eyes color to black") -> 
     &character[1] is {"name": "John Doe", "age": "21", "eyes_color": "black"}.

  ## GET(query)
   Returns a derived view of the current object without modifying it.
   GET() performs semantic retrieval and optional semantic transformation.
   Depending on the target object, it may:
   - select one item;
   - select multiple items;
   - select part of an item;
   - derive a transformed version of the queried content.
   The returned value always preserves the most appropriate type for the
   queried object.

   ### SYNTAX
   [object].GET(query).


   ### PARAMETER
   **query**
   A semantic query expressed as Context Text.
   The query may describe:
   - a selection criterion;
   - requested properties;
   - a semantic transformation;
   - or any combination of these.

   ### RETURN
   Returns a derived result without modifying the source object.
   Return type depends on the target object:

   | Object | Return |
   |---------|--------|
   | @Variable | @Variable |
   | List<T>[] | List<T>[] |
   | &Dataset[] | &Dataset[] |
   | $Macro | $Macro |
   | $Pipeline[] | $Pipeline[] |
   | @FRONTEND.ASSETS[] | Asset Collection |
   | @FRONTEND.CHAT.TEXT() | @Variable |
   
   When applied to collections, GET() returns a filtered collection of the
   same type whenever possible.

   ### BEHAVIOR
   GET() never modifies the source object.
   When a transformation is requested, a new derived object is returned.
   When applied to collections:
   - Items matching the query are returned.
   - Item order is preserved.
   - The original collection is never modified.
   
   When applied to single objects:
   - The query derives a transformed view of that object.
   - The original object remains unchanged.

   ### SUPPORTED OBJECTS
   - @Variable
   - List<T>[]
   - &Dataset[]
   - $Macro
   - $Pipeline[]
   - @FRONTEND.ASSETS[]
   - @FRONTEND.CHAT.TEXT()

   ### EXAMPLES
   Variable transformation
   @text = "Just a new Test"
   @result = @text.GET("remove Test") -> "Just a new"
   Macro transformation
   $pipeline[] = {
      1. Dress &character[1] with &clothing[1].
      2. Dress &character[1] with $shoes[1].
    }
   $newMacro =
   $pipeline[1].GET(
      "replace &clothing[1] with &clothing[2]"
   ) -> Dress &character[1] with &clothing[2].
   Dataset transformation
   &character[].ADD({
      "name":"John Doe",
      "age":"21",
      "eyes_color":"green"
   })

   &newCharacter[].ADD(
      &character[1].GET(
        "replace eyes color with black"
      )
    ) -> { "name":"John Doe", "age":"21", "eyes_color":"black" }
   Dataset selection
   &character[].GET("green eyes") -> Returns a filtered &Dataset[] containing only matching items.

   Asset selection
   @FRONTEND.ASSETS[].GET("image files of clothing") -> Returns an Asset Collection containing only matching assets.

   Pipeline selection
   $quality_pipeline[].GET("instructions about skin") -> Returns a $Pipeline[] containing only the matching Macro items.

  ## ADD(content)
   Appends one or more items to a collection object.
   Returns the identifier of the newly created item when a single item is
   added, or the identifiers of all appended items when multiple items are added.
   
    ### SYNTAX
     mylist[].ADD(value)
     &dataset[].ADD(JSON)
     $pipeline[].ADD($macro)
     $workflow().ADD($pipeline[])
          collection.ADD({ 
                     item1
                     item2
                     })
  
   ### CONTENT
     The `content` parameter may be:
     **Single Item**
     Adds one item to the target collection.
     Supported types:
     - String
     - Number
     - JSON
     - $Macro
     - $Pipeline[]
     
     **Block**
     A block (`{}`) containing one or more compatible items.
     Each top-level item inside the block is processed sequentially.
     The Runtime automatically expands the block into individual ADD()
     operations, preserving item order.
     This behavior follows the general DIRECT COLLECTION ASSIGNMENT rules.
 
   ### BEHAVIOR
   When `content` is a single item:
   - Appends one new item.
   When `content` is a block:
   - Iterates through every top-level item.
   - Performs one ADD() operation for each item.
   - Preserves the order of the block.
   - Continues until every item has been appended.
   If an individual item is incompatible with the target collection, that
   item is ignored while processing continues for the remaining items.
   No error is raised unless the collection itself cannot accept the operation.

   ### RETURN
   **Single item**
   Returns the identifier of the appended item.
   - List<T>[] → index
   - &Dataset[] → index
   - $Pipeline[] → index
   - $Workflow() → generated Pipeline id
   
   **Block**
   Returns the identifiers of every appended item in insertion order.

   ### SUPPORTED OBJECTS
   - List<T>[]
   - &Dataset[]
   - $Pipeline[]
   - $Workflow()

   ### EXAMPLES
   Single values
   mylist[].ADD("test")
   mylist[].ADD(21)

   **Dataset**
   &dataset[].ADD({
      "name":"John Doe",
      "age":"18",
      "eyes_color":"green"
    })

   **Pipeline**
   $pipeline[].ADD($dress)
   $pipeline[].ADD($shoes)

   **Workflow**
   $workflow().ADD($dress_character[])

   **Batch Pipeline**
   $pipeline[].ADD({
      $dress
      $shoes
      $lighting
    }) -> Equivalent to
    $pipeline[].ADD($dress)
    $pipeline[].ADD($shoes)
    $pipeline[].ADD($lighting)

   **Batch Workflow**
   $workflow().ADD({
      $dress_character[]
      $quality_pipeline[]
      $render_pipeline[]
    }) -> Equivalent to
   $workflow().ADD($dress_character[])
   $workflow().ADD($quality_pipeline[])
   $workflow().ADD($render_pipeline[])
  ...

 ## EMBED(assetSource, jsonFields)
   Creates one or more JSON items from one or more assets and appends
   them to a &Dataset[].

   ### PIPELINE
   For each asset resolved from `assetSource`:
   - Analyze the asset.
   - Create a new JSON object.
   - Store the asset identifier (file name) in the JSON `"id"` field.
   - Store the Dataset insertion order in the JSON `"index"` field.
   - If `jsonFields` is provided, extract only the requested
     information and populate the corresponding JSON fields.
   - If `jsonFields` is omitted, analyze the asset and generate a
     JSON object containing the information pertinent to that asset's
     type (see AUTO-DETECTION BEHAVIOR below).
  - Append the generated JSON object as a new item in the target
    &Dataset[].

   Each processed asset always generates exactly one Dataset item.
   If `assetSource` resolves to multiple assets, the Runtime repeats this
   pipeline sequentially for every asset.

   If an asset cannot be converted into a valid JSON object, that asset is
   ignored. Processing continues with the remaining assets without raising
   an error.

   ### AUTO-DETECTION BEHAVIOR
   Applies only when `jsonFields` is omitted.
   The generated fields depend on the asset type:

   - **Image** — generates a JSON object describing the image content,
     including relevant objects, semantic information and geometric /
     spatial coordinates.

   - **Document** — generates a JSON object describing the document
      structure, topic, sections and other relevant information.

   - **Other asset types** — the Runtime automatically generates the JSON
  fields most appropriate to represent that asset type.

   ### PARAMETERS
     **assetSource**
     One or more assets to process.
     Accepted values include:
     - A single Asset.
     - An Asset Collection.
     - Any Runtime method returning one or more Assets.
     Examples:
     @FRONTEND.ASSETS[1]
     @FRONTEND.ASSETS[]
     @FRONTEND.ASSETS.GET(ID="CHARACTER")
     @FRONTEND.ASSETS.GET("character images")

     **jsonFields**
     Optional.
     Defines which fields will be created in each generated JSON object.
     If omitted, AUTO-DETECTION BEHAVIOR is applied.
     Supported parameter types:
     *Schema*
     A JSON template describing the desired output structure.
     Example:
     &dataset[].EMBED(assetSource,{
        "name":"",
        "age":"",
        "eyes_color":""
    })

     **Query**
     A semantic description specifying which information should be extracted
     from each asset.
     Example:
     &dataset[].EMBED(assetSource,"face and body characteristics")

   ### AUTOMATIC FIELDS
   Every generated Dataset item always contains:
   - `"id"` — the asset file name.
   - `"index"` — the sequential position of the item inside the Dataset.
   These fields are automatically generated, are read-only, and are never
   affected by `jsonFields` or AUTO-DETECTION BEHAVIOR.

   ### SYNTAX
   &dataset[].EMBED(assetSource, jsonFields)

   ### SUPPORTED OBJECTS
   - &Dataset[]
   
   ### EXAMPLES
   Single asset
   &character[].EMBED(
      @FRONTEND.ASSETS[1],
     "skin_tone, eyes_color, gender"
   )-> Creates one Dataset item.
   All attached assets
   &assets[].EMBED(
      @FRONTEND.ASSETS[],
      "asset description"
   ) -> Creates one Dataset item for every attached asset.
   Multiple assets returned by a query
   &character[].EMBED(
      @FRONTEND.ASSETS.GET("character images"),
      "face and body characteristics"
  ) -> Creates one Dataset item for each asset returned by GET().
  Automatic detection
  &dataset[].EMBED(
      @FRONTEND.ASSETS[]
  ) -> Processes every attached asset and automatically generates the most
       appropriate JSON structure for each asset type.

 ## REMOVE()
  Removes an item from a list, &dataset, $Pipeline[], or $Workflow() 
  object.

   ### PARAMETERS
     - No parameters, when called on a specific index: removes that 
       item.
     - `"context"`, when called on $Workflow(): removes the entry 
       whose "id" or "context" matches the given literal string.

   ### RETURNS
   Returns true if any item was removed.
   Returns false otherwise.

   ### SYNTAX
     mylist[1].REMOVE()
     &dataset[1].REMOVE()
     $workflow().REMOVE("dress character")

   ### SUPPORTED OBJECTS
     list[], &dataset[], $pipeline[], $workflow()

   ### EXAMPLES
     mylist[1].REMOVE() -> Removes item 1.
     &dataset[1].REMOVE() -> Removes item 1.
     mylist[].REMOVE() -> Removes all items.
     $workflow().REMOVE("dress character") -> Removes the Pipeline 
     entry matching "dress character".

---

# CONDITION PRECEDENCE AND GROUPING

  ## DEFAULT PRECEDENCE
    When an IF clause is followed by a mix of AND and OR clauses 
    without explicit grouping, AND always binds tighter than OR. This 
    means consecutive AND clauses are evaluated as a single group 
    before being combined with any OR clause.

    NOT binds tighter than both AND and OR. It applies only to the 
    single condition immediately following it, unless that condition 
    is a parenthesized group — in which case NOT applies to the entire 
    group as a unit.

  ## Example 01
     IF: A
     AND: B
     OR: C
     AND: D

     Is evaluated as: (A AND B) OR (C AND D)

     **EXPLICIT GROUPING WITH "()"**:
     To override default precedence or make evaluation order explicit, 
     conditions can be grouped using "()". Parentheses follow the same 
     Parameter delimiter pattern already defined in Data Types.

     Content inside "()" is evaluated first, as a single unit, before 
     being combined with clauses outside the parentheses.

  ## Example 02
     IF: (A AND B) OR (C AND D)

     Same result as default precedence above, but explicit and 
     unambiguous — recommended whenever an IF has more than one 
     connector type (AND mixed with OR).

  ## Example with nested grouping
      IF: (A OR B) AND (C OR D)

      Evaluated as: (A OR B) is resolved first, (C OR D) is resolved 
      first, then the two results are combined with AND.

  ## NEGATION WITH "NOT"
     **PATTERN**
     "IF NOT" negates the truth value of the condition that follows 
     it. NOT is a unary operator — it does not connect two conditions, 
     it inverts a single one.

     **SCOPE**
     - "IF NOT: A" evaluates to true only when A is false.
     - When NOT precedes a parenthesized group, it negates the result 
       of the entire group, not just the first condition inside it.
     - NOT does not chain past AND/OR connectors: "IF NOT: A AND B" 
       is evaluated as "(NOT A) AND B", not "NOT (A AND B)". To negate 
       a whole group, wrap it explicitly in parentheses.

  ## Example 03 — simple negation
      IF NOT: A
      AND: B

      Evaluated as: (NOT A) AND B

  ## Example 04 — negating a group
      IF NOT: (A AND B)
      OR: C

      Evaluated as: (NOT (A AND B)) OR C

  ## Example 05 — negation combined with mixed connectors
      IF NOT: (A OR B)
      AND: (C OR NOT D)

      Evaluated as: (NOT (A OR B)) AND (C OR (NOT D))

  ## RULE
      - Parentheses may be nested.
      - A parenthesized group is always resolved as an independent 
        unit before its result is combined with any condition outside 
        it.
      - Whenever an IF clause mixes AND and OR without parentheses, 
        the DEFAULT PRECEDENCE rule above applies — but explicit 
        parentheses are the preferred style and should be used 
        whenever clarity matters more than brevity.
      - NOT binds tighter than AND and OR, and applies only to the 
        single condition or parenthesized group immediately following 
        it.
      - To negate a multi-condition expression, always wrap it in 
        parentheses before applying NOT — this removes any ambiguity 
        about scope.

---

# LOOP CLAUSES

 ## FOR EACH(variable IN collection)
    Iterates over each item of a collection.
    The loop variable is automatically created at the beginning of 
    each iteration.
    The loop variable is local to the loop body.

    Example:
    FOR EACH(asset IN @FRONTEND.ASSETS)
    {
        IF: asset.CONTENT = "character"
        THEN:
            @characters.ADD(asset.NAME)
    }

   **PROCESS**
   1. Evaluates the collection.
   2. Creates the loop variable.
   3. Assigns the current item.
   4. Executes the block.
   5. Continues until the last item.
   6. Execution resumes after the loop.

   ### BREAK
    Immediately terminates the current loop.
    Execution continues after the loop.

    Example:
    FOR EACH(asset IN @FRONTEND.ASSETS)
    {
        IF: asset.ID = "character"
        THEN:
        {
            @character = asset
            BREAK
        }
    }

   ### CONTINUE
    Immediately skips the remaining instructions of the current 
    iteration.
    Execution continues with the next iteration.

    Example:
    FOR EACH(asset IN @FRONTEND.ASSETS)
    {
          IF: asset.CONTENT.EMPTY()
          THEN: CONTINUE
          ELSE: @valid_assets.ADD(asset)
    }

   ### LOOP SCOPE
   The loop variable exists only during the current iteration.
   After the loop finishes, the loop variable is automatically 
   destroyed.
   The collection argument may be:
   • LIST[] variables
   • @FRONTEND.ASSETS[]
   • Any object implementing Collection.

---

# DESIGN PRINCIPLES
- Explicit over implicit.
- Deterministic execution whenever possible.
- Context is a first-class data type.
- Runtime and project logic are strictly separated.
- Every instruction should have a single responsibility.
---

# EXECUTION MODEL
Modules are executed sequentially.
Declarative syntax is expanded into primitive Runtime operations before execution.
Within a module:

- Instructions inside a "{}" block are executed sequentially.
- Clauses are evaluated in the order they appear.
- Execution always proceeds from top to bottom.
- $Macros, $Pipelines[], and $Workflow()s only execute when explicitly 
  called via EXECUTE().
- Variables only store data.
- $END: Terminates the current project.
- $EXIT: Terminates the current module.
- $NEXT: Terminates the current submodule.
- Execution resumes at the next valid scope according to the command 
  used.
- ERROR terminates the entire execution.
- An instruction is never executed more than once, except when 
  explicitly called again.
---