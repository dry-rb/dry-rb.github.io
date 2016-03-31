---
title: Built-in Predicates
layout: gem-single
---

## Basic

### `none?`

Checks that a key's value is nil.

```ruby
describe 'none?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { none? }
    end
  end

  let(:input) { {sample: nil} }

  it 'as regular ruby' do
    assert input[:sample].nil?
  end

  it 'with dry-validation' do
    assert schema.call(good_input).success?
  end
end
```

### `eql?`

Checks that a key's value is equal to the given value.

```ruby
describe 'eql?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { eql?(1234) }
    end
  end

  let(:input) { {sample: 1234} }

  it 'as regular ruby' do
    assert sample[:input] == 1234
  end

  it 'with dry-validation' do
     assert schema.call(input).success?
  end
end
```

### `key?`

Checks that a key is present in the input.

```ruby
describe 'key?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { key? }
    end
  end

  let(:input) { {sample: 1234} }

  it 'as regular ruby' do
    assert input[:sample]
  end

  it 'with dry-validation' do
    assert schema.call(input).success?
  end
end
```

## Types

### `type?`

Checks that a key's class is equal to the given value.

```ruby
describe 'type?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { type?(Integer) }
    end
  end

  let(:input) { {sample: 1234} }

  it 'as regular ruby' do
    assert input[:sample].class == Integer
  end

  it 'with dry-validation' do
    assert schema.call(good_input).success?
  end
end
```

Shorthand for common Ruby types:
* `str?` equivalent to `type?(String)`
* `int?` equivalent to `type?(Integer)`
* `float?` equivalent to `type?(Float)`
* `decimal?` equivalent to `type?(BigDecimal)`
* `bool?` equivalent to `type?(Boolean)`
* `date?` equivalent to `type?(Date)`
* `time?` equivalent to `type?(Time)`
* `date_time?` equivalent to `type?(DateTime)`
* `array?` equivalent to `type?(Array)`
* `hash?` equivalent to `type?(Hash)`

## Number, String, Collection

### `empty?`

Checks that either the array, string, or hash is empty.

```ruby
describe 'empty?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { empty? }
    end
  end

  it 'with regular ruby' do
    assert !{sample: ""}[:sample].empty?
    assert !{sample: []}[:sample].empty?
    assert !{sample: {}}[:sample].empty?
  end

  it 'with dry-validatoin' do
    assert schema.call(sample: "").success?
    assert schema.call(sample: []).success?
    assert schema.call(sample: {}).success?
  end
end
```

### `filled?`

Checks that either the array, string, or hash is filled.

```ruby
describe 'filled?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { filled? }
    end
  end

  it 'with regular ruby' do
    assert !{sample: "1"}[:sample].empty?
    assert !{sample: [2]}[:sample].empty?
    assert !{sample: {k: 3}}[:sample].empty?
  end

  it 'with dry-validatoin' do
    assert schema.call(sample: "1").success?
    assert schema.call(sample: [2]).success?
    assert schema.call(sample: {k: 3}).success?
  end
end
```

### `gt?`

Checks that the value is greater than the given value.

```ruby
describe 'gt?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { gt?(0) }
    end
  end

  it 'with regular ruby' do
    assert 1 > 0
  end

  it 'with dry-validation' do
    assert schema.call(sample: 1).success?
  end
end
```

### `gteq?`

Checks that the value is greater than or equal to the given value.

```ruby
describe 'gteq?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { gteq?(1) }
    end
  end

  it 'with regular ruby' do
    assert 1 >= 1
  end

  it 'with dry-validation' do
    assert schema.call(sample: 1).success?
  end
end
```

### `lt?`

Checks that the value is less than the given value.

```ruby
describe 'lt?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { lt?(1) }
    end
  end

  it 'with regular ruby' do
    assert 0 < 1
  end

  it 'with dry-validation' do
    assert schema.call(sample: 0).success?
  end
end
```

### `lteq?`

Checks that the value is less than or equal to the given value.

```ruby
describe 'lt?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { lteq?(1) }
    end
  end

  it 'with regular ruby' do
    assert 1 <= 1
  end

  it 'with dry-validation' do
    assert schema.call(sample: 1).success?
  end
end
```

### `max_size?`

Check that an array's size is no less than or equal to the given value.

```ruby
describe 'max_size?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { max_size?(3) }
    end
  end

  it 'with regular ruby' do
    assert [1, 2, 3].size <= 3
  end

  it 'with dry-validation' do
    assert schema.call(sample: [1, 2, 3]).success?
  end
end
```

### `min_size?`

Checks that an array's size is greater than or equal to the given value.

```ruby
describe 'min_size?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { min_size?(3) }
    end
  end

  it 'with regular ruby' do
    assert [1, 2, 3].size >= 3
  end

  it 'with dry-validation' do
    assert schema.call(sample: [1, 2, 3]).success?
  end
end
```

### `size?(int)`

Checks that an array's size is equal to the given value.

```ruby
describe 'size?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { size?(3) }
    end
  end

  it 'with regular ruby' do
    assert [1, 2, 3].size = 3
  end

  it 'with dry-validation' do
    assert schema.call(sample: [1, 2, 3]).success?
  end
end
```

### `size?(range)`

Checks that an array's size is between a range of values.

```ruby
describe 'size?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { size?(0..3) }
    end
  end

  it 'with regular ruby' do
    assert (0..3).include?([1, 2, 3].size)
  end

  it 'with dry-validation' do
    assert schema.call(sample: [1, 2, 3]).success?
  end
end
```

### `format?`

Checks that a string matches a given regular expression.

```ruby
describe 'format?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { format?(/^a/) }
    end
  end

  it 'with regular ruby' do
    assert /^a/ =~ "aa"
  end

  it 'with dry-validation' do
     assert schema.call(sample: "aa").success?
  end
end
```

### `inclusion?`


Checks that a value is included in a given array.

```ruby
describe 'inclusion?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { inclusion?([1,3,5]) }
    end
  end

  it 'with regular ruby' do
    assert [1,3,5].include?(3)
  end

  it 'with dry-validation' do
    assert schema.call(sample: 3).success?
  end
end
```

### `exclusion?`


Checks that a value is excluded from a given array.

```ruby
describe 'exclusion?' do
  let(:schema) do
    Dry::Validation.Schema do
      key(:sample) { exclusion?([1,3,5]) }
    end
  end

  it 'with regular ruby' do
    assert ![1,3,5].include?(2)
  end

  it 'with dry-validation' do
    assert schema.call(sample: 2).success?
  end
end
```
