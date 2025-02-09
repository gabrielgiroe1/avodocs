---
feedbackId: 838
license: advanced
---

# Dynamic filters

The Dynamic filters make it so easy to add multiple, composable, and dynamic filters to the <Index /> view.

The first thing you need to do is add the `filterable: true` attribute to the fields you need to filter through. We use `ransack` behind the scenes so it's essential to configure the `ransackable_attributes` list to ensure that every filterable field is incorporated within it.


```ruby{4-6} [Fields]
class Avo::Resources::Project < Avo::BaseResource
  def fields
    field :name, as: :text
    field :status, as: :status, filterable: true
    field :stage, as: :badge, filterable: true
    field :country, as: :country, filterable: true
  end
end
```

Authorize ransackable_attributes
```ruby{3,11}
class Project < ApplicationRecord
  def self.ransackable_attributes(auth_object = nil)
    ["status", "stage", "country"]
  end
end

# Or authorize ALL attributes at once

class Project < ApplicationRecord
  def self.ransackable_attributes(auth_object = nil)
    authorizable_ransackable_attributes
  end
end
```

This will make Avo add this new "Filters" button to the <Index /> view of your resource.

When the user clicks the button, a new filters bar will appear below enabling them to add filters based on the attributes you marked as filterable.
The user can add multiple filters for the same attribute if they desire so.

## Filter types

There are a few types of filters available for you to use out of the box.

:::option Boolean

### Conditions

 - Is true
 - Is false
 - Is present
 - Is blank
:::

:::option Date

### Conditions

 - Is
 - Is not
 - Is on or before
 - Is on or after
 - Is within
 - Is present
 - Is blank
:::

:::option Has many

This filter will give you options from the database.

### Conditions

 - Is
 - Is not
 - Is present
 - Is blank
:::

:::option Number

### Conditions

 - `=` (equals)
 - `!=` (is different)
 - `>` (greater than)
 - `>=` (greater than or equal to)
 - `<` (lower than)
 - `<=` (lower than or equal to)
 - Is present
 - Is blank
:::

:::option Select

### Conditions

 - Is
 - Is not
 - Is present
 - Is blank
:::

:::option Text

### Conditions

 - Contains
 - Does not contain
 - Is
 - Is not
 - Starts with
 - Ends with
 - Is present
 - Is blank
:::

::::option Tags

### Conditions

 - Are
 - Contain
 - Overlap
 - Contained in

:::warning
This will only work with database array columns, not when using the `acts-as-taggable-on` gem.
:::

::::

## Options

You can have a few customization options available that you can add in your `avo.rb` initializer file.

```ruby
Avo.configure do |config|
  # Other Avo configurations
end

if defined?(Avo::DynamicFilters)
  Avo::DynamicFilters.configure do |config|
    config.button_label = "Advanced filters"
    config.always_expanded = true
  end
end
```

:::option `button_label`
This will change the label on the expand label.
:::

:::option `always_expanded`
You may opt-in to have them always expanded and have the button hidden.
:::

## Field to filter matching

At the moment the filters are not configurable so each field will have a dedicated filter type. We will have a more advanced configuration in the future.

The current field-to-filter matching is done like so:

```ruby
def field_to_filter(type)
  case type.to_sym
  when :boolean
    :boolean
  when :date, :date_time, :time
    :date
  when :id, :number, :progress_bar
    :number
  when :select, :badge, :country, :status
    :select
  when :text, :textarea, :code, :markdown, :password, :trix
    :text
  else
    :text
  end
end
```

## Caveats

At some point we'll integrate the [Basic filters](./basic-filters) into the dynamic filters bar. Until then, if you have both basic and dynamic filters on your resource you'll have two `Filters` buttons on your <Index /> view.

To mitigate that you can toggle the `always_expanded` option to true.
