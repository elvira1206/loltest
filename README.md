# loltest
Этот репозиторий создан для демонстрации
import string
from io import FileIO
from typing import List, Tuple, Union, Dict, TextIO


def read_students(filename="students.csv"):
    with open(filename, "r", encoding="utf-8") as f:
        titles = f.readline().strip().split(",")
        data = []
        errors = []

        for i, line in enumerate(f):
            line = line.strip()
            data.append(tuple(int(entry) if entry.isdigit() else entry for entry in line.split(",")))
            if data[-1][-1] == "None":
                errors.append(i)

    return titles, data, errors


def write_students(data: List[Tuple[Union[int, str]]], titles: List[str], filename="student_new.csv") -> None:
    with open(filename, "w", encoding="utf-8") as f:
        f.write(",".join(titles) + "\n")

        for entry in data:
            f.write(",".join(map(str, entry)) + "\n")


def get_average_score(data: List[Tuple[Union[int, str]]], class_name: str) -> float:
    scores = []

    for entry in data:
        if entry[3] == class_name and isinstance(entry[-1], int):
            scores.append(entry[-1])

    return sum(scores) / len(scores)


def fix_scores(data: List[Tuple[Union[int, str]]], errors: List[int]) -> List[Tuple[Union[int, str, float]]]:
    averages: Dict[str, float] = {}
    data: List[Tuple[Union[int, str, float]]] = data.copy()

    for i in errors:
        entry = list(data[i])
        class_name: str = entry[3]
        if class_name not in averages:
            averages[class_name] = round(get_average_score(data, class_name), 3)

        entry[-1] = averages[class_name]
        data[i] = tuple(entry)

    return data


def insertion_sort(data):
    for i in range(len(data) - 1, -1, -1):
        for j in range(len(data) - i - 1, 0, -1):
            entry = data[j]
            if entry[-1] == "None":
                del data[j]
                data.append(entry)
                continue

            if not isinstance(data[j - 1][-1], int):
                continue

            if data[j - 1][-1] >= data[j][-1]:
                continue

            data[j - 1], data[j] = data[j], data[j - 1]

    return data


def get_initials(name: str):
    name = name.split()
    return f"{name[1][0]}. {name[0]}"


def generate_login(student) -> str:
    name = student[1].split()
    return f"{name[0]}_{name[1][0]}{name[2][0]}"


def generate_password(student) -> str:
    alphabet = string.digits + string.ascii_letters
    password = ""


def generate_and_write_accounts(input_filename: str, out_filename: str):
    titles, data, _ = read_students(input_filename)

    for i, entry in enumerate(data):
        data[i] = (*entry, generate_login(entry), generate_password(entry))

    write_students(data, titles, out_filename)


def ex1():
    print("Ex1")

    titles, data, errors = read_students()
    new_data = fix_scores(data, errors)

    for entry in new_data:
        if entry[1].startswith("Хадаров Владимир"):
            print(f"Ты получил: {entry[-1]}, за проект - {entry[2]}")
            break

    write_students(new_data, titles)
    print()


def ex2():
    print("Ex2")

    titles, data, _ = read_students()
    classes_10 = list(filter(lambda a: a[3][:-1] == "10", data))
    sorted_data = insertion_sort(classes_10)
    # write_students(sorted_data, titles, filename="st2.csv")

    for i in range(3):
        print(f"{i + 1} место: {get_initials(sorted_data[i][1])}")

    print()


def ex3():
    titles, data, _ = read_students()

    while True:
        project_id = input("Enter project id: ")
        if project_id == "СТОП":
            break
        else:
            project_id = int(project_id)

        success = False
        for entry in data:
            if entry[2] == project_id:
                name = get_initials(entry[1])
                score = entry[-1]
                print(f"Проект № {project_id} делал: {name} он(а) получил(а) оценку - {score}.")
                success = True
                break

        if not success:
            print(f"Проект не найден!")


def ex4():
    generate_and_write_accounts("students.csv", "students_password.csv")


def main():
    ex1()
    ex2()
    ex3()
    ex4()


if __name__ == '__main__':
    main()
