# bitset
По умолчанию значения контейнера 0, можно передать строку состоящую из 0 и 1, что бы конструктор преобразовал ее в обратном порядке, т.е. если `str="001"`, то `bitset=100`.

```cpp
int main()
{
	bitset<10> myBitset;		//0000000000
	myBitset.set(3);			//0001000000
	myBitset.set(6);			//0001001000
	myBitset[8] = true;			//0001001010
	myBitset[9] = myBitset[3];	//0001001011
	if (myBitset.test(3)) {
		cout << "Bit 3 is set!" << endl;
	}
	cout << myBitset << endl;

	auto str1{ "0011001100" };
	auto str2{ "0000111100" };
	bitset<10> bitsOne{ str1 };//0011001100
	bitset<10> bitsTwo{ str2 };//0011110000

	auto bitsThree{ bitsOne & bitsTwo };//0011000000
	
	bitsThree <<= 4;					//0000001100
}
```

```cpp
class CableCompany final
{
public:
	// Number of supported channels.
	static const size_t NumChannels{ 10 };
	// Adds package with the channels specified as a bitset to the database.
	void addPackage(std::string_view packageName,
		const std::bitset<NumChannels>& channels);
	// Adds package with the channels specified as a string to the database.
	void addPackage(std::string_view packageName, std::string_view channels);
	// Removes the specified package from the database.
	void removePackage(std::string_view packageName);
	// Retrieves the channels of a given package.
	// Throws out_of_range if the package name is invalid.
	const std::bitset<NumChannels>& getPackage(
		std::string_view packageName) const;
	// Adds customer to database with initial channels found in package.
// Throws out_of_range if the package name is invalid.
// Throws invalid_argument if the customer is already known.
	void newCustomer(std::string_view name, std::string_view package);
	// Adds customer to database with given initial channels.
	// Throws invalid_argument if the customer is already known.
	void newCustomer(std::string_view name,
		const std::bitset<NumChannels>& channels);
	// Adds the channel to the customer's profile.
	// Throws invalid_argument if the customer is unknown.
	void addChannel(std::string_view name, int channel);
	// Removes the channel from the customer's profile.
	// Throws invalid_argument if the customer is unknown.
	void removeChannel(std::string_view name, int channel);
	// Adds the specified package to the customer's profile.
	// Throws out_of_range if the package name is invalid.
	// Throws invalid_argument if the customer is unknown.
	void addPackageToCustomer(std::string_view name,
		std::string_view package);
	// Removes the specified customer from the database.
	void deleteCustomer(std::string_view name);
	// Retrieves the channels to which a customer subscribes.
	// Throws invalid_argument if the customer is unknown.
	const std::bitset<NumChannels>& getCustomerChannels(
		std::string_view name) const;
private:
	// Retrieves the channels for a customer. (non-const)
	// Throws invalid_argument if the customer is unknown.
	std::bitset<NumChannels>& getCustomerChannelsHelper(
		std::string_view name);
	using MapType = std::map<std::string, std::bitset<NumChannels>>;
	MapType m_packages, m_customers;
};

void CableCompany::addPackage(string_view packageName,
	const bitset<NumChannels>& channels)
{
	m_packages.emplace(packageName, channels);
}
void CableCompany::addPackage(string_view packageName, string_view channels)
{
	addPackage(packageName, bitset<NumChannels> { channels.data() });
}
void CableCompany::removePackage(string_view packageName)
{
	m_packages.erase(packageName.data());
}
const bitset<CableCompany::NumChannels>& CableCompany::getPackage(
	string_view packageName) const
{
	// Get a reference to the specified package.
	if (auto it{ m_packages.find(packageName.data()) }; it != end(m_packages)) {
		// Found package. Note that 'it' is a reference to a name/bitset pair.
		// The bitset is the second field.
		return it->second;
	}
	throw out_of_range{ "Invalid package" };
}
void CableCompany::newCustomer(string_view name, string_view package)
{
	// Get the channels for the given package.
	auto& packageChannels{ getPackage(package) };
	// Create the account with the bitset representing that package.
	newCustomer(name, packageChannels);
}
void CableCompany::newCustomer(string_view name,
	const bitset<NumChannels>& channels)
{
	// Add customer to the customers map.
	if (auto [iter, success] { m_customers.emplace(name, channels) }; !success) {
		// Customer was already in the database. Nothing changed.
		throw invalid_argument{ "Duplicate customer" };
	}
}
void CableCompany::addChannel(string_view name, int channel)
{
	// Get the current channels for the customer.
	auto& customerChannels{ getCustomerChannelsHelper(name) };
	// We found the customer; set the channel.
	customerChannels.set(channel);
}
void CableCompany::removeChannel(string_view name, int channel)
{
	// Get the current channels for the customer.
	auto& customerChannels{ getCustomerChannelsHelper(name) };
	// We found this customer; remove the channel.
	customerChannels.reset(channel);
}
void CableCompany::addPackageToCustomer(string_view name, string_view package)
{
	// Get the channels for the given package.
	auto& packageChannels{ getPackage(package) };
	// Get the current channels for the customer.
	auto& customerChannels{ getCustomerChannelsHelper(name) };
	// Or-in the package to the customer's existing channels.
	customerChannels |= packageChannels;
}
void CableCompany::deleteCustomer(string_view name)
{
	m_customers.erase(name.data());
}
const bitset<CableCompany::NumChannels>& CableCompany::getCustomerChannels(
	string_view name) const
{
	// Find a reference to the customer.
	if (auto it{ m_customers.find(name.data()) }; it != end(m_customers)) {
		// Found customer. Note that 'it' is a reference to a name/bitset pair.
		// The bitset is the second field.
		return it->second;
	}
	throw invalid_argument{ "Unknown customer" };
}
bitset<CableCompany::NumChannels>& CableCompany::getCustomerChannelsHelper(
	string_view name)
{
	// Forward to const getCustomerChannels() to avoid code duplication.
	return const_cast<bitset<NumChannels>&>(
		as_const(*this).getCustomerChannels(name));
}
int main()
{
	CableCompany myCC;
	myCC.addPackage("basic", "1111000000");
	myCC.addPackage("premium", "1111111111");
	myCC.addPackage("sports", "0000100111");
	myCC.newCustomer("Marc G.", "basic");
	myCC.addPackageToCustomer("Marc G.", "sports");
	cout << myCC.getCustomerChannels("Marc G.") << endl;//1111100111
}
```

#do/review Удалить очевидные комментарии из кода выше