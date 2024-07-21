# SwiftUI Project

This project is a SwiftUI application that demonstrates a list of items with various functionalities such as adding, editing, deleting, and viewing details. The project also includes a page tab view and context menus for additional actions.

## Code

```swift
import SwiftUI

struct ContenntView: View {
    @State private var items = [
        Item(id: UUID(), name: "Ferrari", description: "A fast car.", imageName: "tt"),
        Item(id: UUID(), name: "Toyota", description: "A luxury bag.", imageName: "gg"),
        Item(id: UUID(), name: "Tesla", description: "An electric car.", imageName: "ee")
    ]
    @State private var newItemName = ""
    @State private var selectedItem: Item?
    @State private var showDetails = false
    @State private var editingIndex: Int?
    @State private var newText: String = ""

    var body: some View {
        NavigationView {
            VStack {
                PageTabView()
                    .frame(height: 200)
                    .tabViewStyle(PageTabViewStyle(indexDisplayMode: .always))
                    .indexViewStyle(PageIndexViewStyle(backgroundDisplayMode: .always))

                HStack {
                    TextField("Add new item", text: $newItemName)
                        .textFieldStyle(RoundedBorderTextFieldStyle())
                        .padding()
                        .background(Color.white)
                        .cornerRadius(10)
                        .shadow(color: .gray.opacity(0.3), radius: 3, x: 0, y: 3)
                    Button(action: {
                        withAnimation {
                            addItem()
                        }
                    }) {
                        Text("Add")
                            .foregroundColor(.white)
                            .padding(.horizontal)
                            .padding(.vertical, 8)
                            .background(Color.green)
                            .cornerRadius(10)
                            .shadow(color: .green.opacity(0.7), radius: 3, x: 0, y: 3)
                    }
                }
                .padding()

                List {
                    ForEach(items.indices, id: \.self) { index in
                        if editingIndex == index {
                            HStack {
                                TextField("Edit item", text: $newText, onCommit: {
                                    withAnimation {
                                        items[index].name = newText
                                        editingIndex = nil
                                    }
                                })
                                .textFieldStyle(RoundedBorderTextFieldStyle())
                                .padding()
                                .background(Color.white)
                                .cornerRadius(10)
                                .shadow(color: .gray.opacity(0.3), radius: 3, x: 0, y: 3)
                                Button(action: {
                                    withAnimation {
                                        items[index].name = newText
                                        editingIndex = nil
                                    }
                                }) {
                                    Text("Save")
                                        .foregroundColor(.white)
                                        .padding(.horizontal)
                                        .padding(.vertical, 8)
                                        .background(Color.blue)
                                        .cornerRadius(10)
                                        .shadow(color: .blue.opacity(0.7), radius: 3, x: 0, y: 3)
                                }
                            }
                        } else {
                            HStack {
                                Image(items[index].imageName)
                                    .resizable()
                                    .frame(width: 50, height: 50)
                                    .clipShape(Circle())
                                    .shadow(radius: 3)
                                Text(items[index].name)
                                    .padding()
                                    .background(Color.white)
                                    .cornerRadius(10)
                                    .shadow(color: .gray.opacity(0.3), radius: 3, x: 0, y: 3)
                                Spacer()
                            }
                            .padding(.vertical, 5)
                            .onTapGesture {
                                withAnimation {
                                    selectedItem = items[index]
                                    showDetails = true
                                }
                            }
                            .contextMenu {
                                Button(action: {
                                    startEditing(at: index)
                                }) {
                                    Label("Edit", systemImage: "pencil")
                                }
                                Button(action: {
                                    withAnimation {
                                        deleteItem(at: index)
                                    }
                                }) {
                                    Label("Delete", systemImage: "trash")
                                }
                                Button(action: {
                                    shareItem(items[index].name)
                                }) {
                                    Label("Share", systemImage: "square.and.arrow.up")
                                }
                                Button(action: {
                                    selectedItem = items[index]
                                    showDetails = true
                                }) {
                                    Label("Details", systemImage: "info.circle")
                                }
                            }
                        }
                    }
                    .onDelete(perform: delete)
                    .onMove(perform: move)
                }
                .listStyle(InsetGroupedListStyle())
                .navigationTitle("Cars Detail")
                .navigationBarItems(trailing: EditButton())
            }
            .background(
                LinearGradient(gradient: Gradient(colors: [Color.purple.opacity(0.3), Color.blue.opacity(0.3)]), startPoint: .topLeading, endPoint: .bottomTrailing)
                    .edgesIgnoringSafeArea(.all)
            )
            .sheet(isPresented: $showDetails) {
                if let selectedItem = selectedItem {
                    DetailsView(item: selectedItem)
                }
            }
        }
    }

    private func addItem() {
        if !newItemName.isEmpty {
            items.append(Item(id: UUID(), name: newItemName, description: "New item description", imageName: "placeholder"))
            newItemName = ""
        }
    }

    private func startEditing(at index: Int) {
        editingIndex = index
        newText = items[index].name
    }

    private func deleteItem(at index: Int) {
        items.remove(at: index)
    }

    private func delete(at offsets: IndexSet) {
        items.remove(atOffsets: offsets)
    }

    private func move(from source: IndexSet, to destination: Int) {
        items.move(fromOffsets: source, toOffset: destination)
    }

    private func shareItem(_ item: String) {
        print("Share \(item)")
    }
}

struct DetailsView: View {
    var item: Item

    var body: some View {
        VStack {
            Image(item.imageName)
                .resizable()
                .scaledToFit()
                .frame(height: 200)
                .cornerRadius(15)
                .shadow(radius: 10)
                .padding()
            Text("Details for \(item.name)")
                .font(.largeTitle)
                .padding()
            Text(item.description)
                .font(.body)
                .padding()
            Spacer()
        }
        .background(Color.yellow.opacity(0.3))
        .cornerRadius(10)
        .padding()
    }
}

struct PageTabView: View {
    let images = ["qq", "uu", "ee", "oo", "ii", "gg"]

    var body: some View {
        TabView {
            ForEach(images, id: \.self) { imageName in
                VStack {
                    Image(imageName)
                        .resizable()
                        .scaledToFit()
                        .cornerRadius(15)
                        .padding()
                }
                .frame(maxWidth: .infinity, maxHeight: .infinity)
                .cornerRadius(15)
                .padding()
            }
        }
    }
}

struct Item: Identifiable {
    let id: UUID
    var name: String
    var description: String
    var imageName: String
}

struct ContenntView_Previews: PreviewProvider {
    static var previews: some View {
        ContenntView()
    }
}
