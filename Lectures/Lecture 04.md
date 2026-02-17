%% Was travelling, todo later

``` Java
boolean swapped;
int j = size;

do {
	swapped = false;
	for (int i=0; i+1<j; i++) {
		if (values[i] > values[i+1]) {
			int v = values[i];
			values[i] = values[i+1]
			values[i+1] = v;
			swapped = true;
		}
		j--;
	}
} while (swapped)
```
