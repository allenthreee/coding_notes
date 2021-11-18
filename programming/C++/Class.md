## private  & protected

| The class members declared as private can be accessed only by the functions inside the class. <br />private成员只能被class中的对象访问，子类都不能访问 | Protected access modifier is similar to that of private access modifiers. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Only the member functions or the friend functions are allowed to access the private data members of a class.<br />friend function也可以访问 | The class member declared as Protected are inaccessible outside the class but they can be accessed by any subclass(derived class) of that class.<br />子类可以访问父类的protected成员，因为子类object的创建其实就是先创建父类，再在这个object的外面套一层这个子类的性质 |