```

test('renders SearchBar and updates searchTerm on text change', () => {
  const searchBarJSX = wrapper.instance().renderSearchBar();
  const viewWrapper = shallow(searchBarJSX);

  // Grab the SearchBar (assumed to be first child)
  const searchBar = viewWrapper.children().first();

  expect(searchBar).toBeDefined();
  expect(searchBar.prop('value')).toBe('');

  // Simulate text input
  searchBar.prop('onChangeText')('void');
  expect(wrapper.state('searchTerm')).toBe('void');
});



```
