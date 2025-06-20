```

test('clears searchTerm on onClosePress', () => {
  // Set initial state
  wrapper.setState({ searchTerm: 'test' });

  const searchBarJSX = wrapper.instance().renderSearchBar();
  const viewWrapper = shallow(searchBarJSX);

  // Grab the SearchBar component
  const searchBar = viewWrapper.children().first();

  expect(searchBar).toBeDefined();

  // Simulate clear icon press
  searchBar.prop('onClosePress')();
  expect(wrapper.state('searchTerm')).toBe('');
});


```
