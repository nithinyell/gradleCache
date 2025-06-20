```

test('renders SearchBar and updates searchTerm on text change', () => {
  const searchBarJSX = wrapper.instance().renderSearchBar();

  // shallow renders the outer <View> from renderSearchBar
  const viewWrapper = shallow(searchBarJSX);

  // find SearchBar inside the view
  const searchBar = viewWrapper.find('SearchBar');
  expect(searchBar.exists()).toBe(true);

  expect(searchBar.prop('value')).toBe('');

  searchBar.prop('onChangeText')('void');
  expect(wrapper.state('searchTerm')).toBe('void');
});

test('clears searchTerm on onClosePress', () => {
  wrapper.setState({ searchTerm: 'test' });

  const searchBarJSX = wrapper.instance().renderSearchBar();
  const viewWrapper = shallow(searchBarJSX);
  const searchBar = viewWrapper.find('SearchBar');

  expect(searchBar.exists()).toBe(true);

  searchBar.prop('onClosePress')();
  expect(wrapper.state('searchTerm')).toBe('');
});


```
