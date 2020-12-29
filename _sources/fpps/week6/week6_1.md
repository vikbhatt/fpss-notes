# Other Collections


We have seen that lists are **linear**: Access to the first element is much faster than access to the middle or end of a list. The Scala library also defines an alternative sequence implementation, `Vector`.
This one has more evenly balanced access patterns than `List`. `Vector` is implemented as very very shallow trees. A vector of 32 elements is just an array (stored in sequence). If a vector becomes larger than 32, you would have vector of 32 pointer to arrays of 32 elements. Once we filled 32*32 you would have pointers to pointers to arrays of 32 elements. Each level would go deeper. 

How much time does it take to retrieve for an element?

The number of access are the depth of the vector. A depth of 6 would give billion elements (actually it's $log_{32}(N)$)

