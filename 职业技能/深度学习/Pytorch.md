# Module

## 魔法方法

###  _call__

```python
model = torch.nn.Module()
model() # --> model.forward()
```

### _setattr__

- Modules

```python
class model(torch.nn.Module):
    def __init__(self):
		self.linear = torch.nn.Module()
        #-->linear = torch.nn.Module()
        #-->self._modules.add(linear)
        #-->self.linear = linear
```

- Parameter

```python
class model(torch.nn.Module):
    def __init__(self):
		self.weight = Parameter()
        #-->weight = Parameter()
        #-->self._parameters.add(weight)
        #-->self.weight = weight
```

# Optimizer

## param_groups

```
self.param_groups = {
	"param_group" = {
		pm1,
		pm2,
		...
	}
}
```

## zero_grad

## step

# _Loss

## forward

## backward

