```
test('renders NavigationHeader with translated title', () => {
  const wrapper = shallow(<AccountsListScreen {...mockProps} />);
  const header = findByTestAttr(wrapper, 'NavigationHeader');
  expect(header.exists()).toBe(true);
  expect(header.prop('navigationTitle')).toBe('VOID_CHEQUE.DownloadVoidCheques');
});

test('shows API error message when accountsListError is true', () => {
  const props = {
    ...mockProps,
    voidCheque: {
      ...mockProps.voidCheque,
      accountsListError: true
    }
  };
  const wrapper = shallow(<AccountsListScreen {...props} />);
  const errorComponent = findByTestAttr(wrapper, 'api-error');
  expect(errorComponent.exists()).toBe(true);
  expect(errorComponent.dive().text()).toContain('VOID_CHEQUE.API_ERROR');
});

test('shows empty accounts message when no accounts', () => {
  const wrapper = shallow(<AccountsListScreen {...mockProps} />);
  wrapper.setState({ isEmptyAccountList: true });
  const errorComponent = findByTestAttr(wrapper, 'no-accounts-error');
  expect(errorComponent.exists()).toBe(true);
  expect(errorComponent.dive().text()).toContain('VOID_CHEQUE.NO_ACCOUNTS_FOUND_ERROR');
});

test('shows search error message when no results match search term', () => {
  const wrapper = shallow(<AccountsListScreen {...mockProps} />);
  wrapper.setState({ searchError: true });
  const errorComponent = findByTestAttr(wrapper, 'search-error');
  expect(errorComponent.exists()).toBe(true);
  expect(errorComponent.dive().text()).toContain('VOID_CHEQUE.SEARCH_ERROR');
});
```
